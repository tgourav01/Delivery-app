
# FOODZEE

### *Order food. Pay securely. Run your restaurant. Manage your platform.*

**A full-stack online food delivery platform built with Spring Boot & React.js**

[![Java](https://img.shields.io/badge/Java-17-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)](https://www.java.com/)
[![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.x-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)](https://spring.io/projects/spring-boot)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-5-646CFF?style=for-the-badge&logo=vite&logoColor=white)](https://vitejs.dev/)
[![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![Razorpay](https://img.shields.io/badge/Razorpay-Test_Mode-02042B?style=for-the-badge&logo=razorpay&logoColor=white)](https://razorpay.com/)
[![Maven](https://img.shields.io/badge/Maven-C71A36?style=for-the-badge&logo=apache-maven&logoColor=white)](https://maven.apache.org/)

---

</div>

## 📖 About

**FOODZEE**, full-stack food delivery web application featuring three distinct user roles, a complete restaurant approval workflow, Razorpay test-mode payment with server-side signature verification, admin-controlled platform governance, and a polished dark/light theme system.

Built as a full-stack project, NOMZEE follows a clean three-tier architecture:

-  **Presentation Layer** — React.js 18 + Vite 5 with CSS variable theming
-  **Business Logic Layer** — Spring Boot 3.x REST API (25+ endpoints)
-  **Data Layer** — MySQL 8.0 via Spring Data JPA / Hibernate (7 tables)

---

##  Features

###  For Customers
- Register and log in as a **CUSTOMER**
- Browse the full menu with live **search** across food name, restaurant, and description
- **Add to cart** with quantity controls (+/− per item, reset after each add)
- View order summary
- Pay securely via **Razorpay test-mode** — card, UPI, netbanking
- Server-side **HMAC SHA256 signature verification** before order placement
- Track orders with an **animated 4-step progress tracker**
  - Order Placed → Preparing → On the Way (10 min) → Delivered (20 min)
- View full **order history** with item breakdown and payment ID
- Food items from **blocked restaurants** show as unavailable automatically

###  For Restaurant Owners (BUSINESS)
- Register and log in as a **BUSINESS** user
- Submit restaurants for **admin approval** — starts as PENDING
- Dashboard shows real-time status: **PENDING / APPROVED / REJECTED / BLOCKED**
- Food items can only be added to **APPROVED** restaurants
- Add food items with **drag & drop image upload** (auto-cropped 1:1 via HTML Canvas)
- **Edit** food name, price, description, and image inline
- **Delete** food items with confirmation modal
- **Toggle availability** — instantly greyed out in the customer menu
- **Manage Menu tab** loads ALL your foods from the database (not just current session)
- Strict **owner-filtering** — you can only see and manage your own restaurant's items

###  For Admin
- **Approvals Tab** — Review all restaurant registration requests
  - Approve → restaurant goes APPROVED, owner can now add food
  - Reject → with optional reason stored and shown to owner
- **Users Tab** — View all customers and business owners
  - Block → user gets error on next login, session denied
  - Unblock → restores full access
- **Restaurants Tab** — View all restaurants with owner details
  - Block → all food items of that restaurant become unavailable to customers
  - Unblock → food items restore to normal
  - Approve / Reject PENDING restaurants directly from this tab too
- Role verified from **database on every admin API call** — session cannot be spoofed


### 4. Start the Backend

```bash
cd food-backend
mvn spring-boot:run
```

Backend starts at **http://localhost:8080**. Tables auto-created by Hibernate on first run.

---

### 5. Create Admin User

After the backend starts (tables created), run this SQL once:

```sql
INSERT INTO user (name, email, password, role, is_blocked)
VALUES ('Admin', 'admin@nomzee.com', 'admin123', 'ADMIN', 0);
```

> If `is_blocked` column is missing: `ALTER TABLE user ADD COLUMN is_blocked TINYINT(1) DEFAULT 0;`
> If `status` column missing on restaurant: `ALTER TABLE restaurant ADD COLUMN status VARCHAR(20) DEFAULT 'PENDING';`

---

### 6. Start the Frontend

```bash
cd rest-api-frontend
npm install
npm run dev
```

Frontend starts at **http://localhost:5173**

> Vite proxy auto-forwards all API calls (`/auth`, `/food`, `/cart`, `/order`, `/restaurant`, `/admin`, `/payment`) to `http://localhost:8080`.

---

##  API Reference

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| `POST` | `/auth/register` | Register CUSTOMER or BUSINESS | None |
| `POST` | `/auth/login` | Login — creates HTTP session | None |
| `GET` | `/auth/me` | Get real user + role from DB | Session |
| `GET` | `/food/all` | Get all food items | None |
| `POST` | `/food/add` | Add food (APPROVED restaurant only) | Session |
| `PUT` | `/food/update/{id}` | Edit food item | Session |
| `DELETE` | `/food/delete/{id}` | Delete food item | Session |
| `POST` | `/restaurant/add` | Register restaurant → PENDING + approval record | Session |
| `GET` | `/restaurant/all` | Get all restaurants with status | None |
| `GET` | `/restaurant/my` | Get owner's restaurants only | Session |
| `POST` | `/cart/add` | Add `{foodId, qty}` to cart | Session |
| `GET` | `/cart/view` | View cart | Session |
| `POST` | `/order/place` | Place order `{total}` — clears cart | Session |
| `GET` | `/order/my` | Get order history | Session |
| `GET` | `/admin/all-approvals` | All restaurant approval records | Admin |
| `GET` | `/admin/all-users` | All non-admin users | Admin |
| `GET` | `/admin/all-restaurants` | All restaurants | Admin |
| `POST` | `/admin/approve-restaurant/{id}` | Approve → APPROVED | Admin |
| `POST` | `/admin/reject-restaurant/{id}` | Reject `{reason}` | Admin |
| `POST` | `/admin/block-restaurant/{id}` | Block → foods unavailable | Admin |
| `POST` | `/admin/unblock-restaurant/{id}` | Unblock restaurant | Admin |
| `POST` | `/admin/block-user/{id}` | Block user → login denied | Admin |
| `POST` | `/admin/unblock-user/{id}` | Unblock user | Admin |
| `POST` | `/payment/create-order` | Create Razorpay order `{amount}` | Session |
| `POST` | `/payment/verify` | Verify HMAC + place order | Session |

---
