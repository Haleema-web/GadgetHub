  GadgetHub — Electronic Gadgets Store

**Group No. 03 — BTech Information Technology — OOP Project**

A full-stack electronics marketplace built to demonstrate **Inheritance, Method
Overriding, Runtime Polymorphism and Dynamic Binding** through a real,
working e-commerce application (not just a UI mockup).

---

## 1. Project Structure

```
gadgethub/
├── backend/                  Spring Boot REST API (Java 17)
│   ├── pom.xml
│   └── src/main/java/com/gadgethub/
│       ├── model/            Gadget hierarchy + all JPA entities
│       ├── repository/       Spring Data JPA repositories
│       ├── service/          Business logic (incl. PolymorphismDemoService)
│       ├── controller/       REST controllers
│       ├── security/         JWT filter, util, UserDetailsService
│       ├── config/           SecurityConfig, DataSeeder
│       ├── dto/               Request/response payloads
│       └── exception/        Global error handling
├── frontend/                 React + Vite + Tailwind CSS
│   └── src/
│       ├── components/       Header, ProductCard, route guards, etc.
│       ├── pages/            Home, ProductListing, ProductDetail, Cart, Checkout,
│       │                     Login, Register, Orders, Wishlist, OopConcepts,
│       │                     About, AdminDashboard, AdminProducts, AdminProductForm, AdminOrders
│       ├── context/           AuthContext, CartContext, ToastContext
│       ├── services/          Axios-based API clients
│       └── layouts/           MainLayout (Header + Footer)
└── database/
    └── schema.sql            Reference schema (Hibernate creates this automatically)
```

---

## 2. Technologies Used

| Layer     | Stack |
|-----------|-------|
| Frontend  | React 18, Vite, React Router, Tailwind CSS, Axios |
| Backend   | Java 17, Spring Boot 3, Spring Web, Spring Data JPA, Spring Security |
| Auth      | JWT (jjwt), BCrypt password hashing |
| Database  | MySQL 8 |
| Build     | Maven (backend), npm/Vite (frontend) |

---

## 3. Database Setup

You only need MySQL installed and running. Hibernate creates and updates the
schema automatically (`spring.jpa.hibernate.ddl-auto=update`), and sample
data is seeded automatically on first run.

```sql
CREATE DATABASE electronic_gadgets_store;
```

That's it — no need to run `database/schema.sql` by hand (it's provided
purely as documentation of the resulting schema for your report/viva).

Set your MySQL credentials as environment variables before starting the
backend (defaults are `root` / `root`):

```bash
export DB_USERNAME=root
export DB_PASSWORD=your_mysql_password
```

---

## 4. Backend — Run Commands

```bash
cd backend
mvn clean install
mvn spring-boot:run
```

The API starts on **http://localhost:8080**. On first run you'll see:

```
>>> Seeding GadgetHub sample data...
>>> Seeding complete: 37 products created.
```

## 5. Frontend — Run Commands

```bash
cd frontend
npm install
npm run dev
```

The app starts on **http://localhost:5173** and proxies `/api` calls to the
backend at `localhost:8080` (configured in `vite.config.js`).

---

## 6. Default Demo Credentials

| Role  | Email | Password |
|-------|-------|----------|
| Admin | admin@gadgethub.com | Admin@123 |
| User  | user@gadgethub.com  | User@123  |

⚠️ **Demo credentials only** — change these before any real deployment.

---

## 7. API Summary

```
Auth
  POST   /api/auth/register
  POST   /api/auth/login

Gadgets (public GET, admin-only mutations)
  GET    /api/gadgets                 ?category&brand&minPrice&maxPrice&minRating&inStockOnly&sortBy
  GET    /api/gadgets/{id}
  GET    /api/gadgets/search          ?keyword=
  GET    /api/gadgets/category/{name}
  GET    /api/gadgets/bestsellers
  GET    /api/gadgets/{id}/related
  POST   /api/gadgets                 (ADMIN)
  PUT    /api/gadgets/{id}            (ADMIN)
  DELETE /api/gadgets/{id}            (ADMIN)

Categories
  GET    /api/categories

Cart (authenticated)
  GET    /api/cart
  POST   /api/cart
  PUT    /api/cart/{itemId}
  DELETE /api/cart/{itemId}
  DELETE /api/cart

Orders (authenticated)
  POST   /api/orders                  checkout
  GET    /api/orders                  my orders
  GET    /api/orders/{id}

Reviews
  GET    /api/gadgets/{id}/reviews
  POST   /api/gadgets/{id}/reviews    (authenticated)

Admin (ADMIN only)
  GET    /api/admin/stats
  GET    /api/admin/orders
  PUT    /api/admin/orders/{id}/status
  GET    /api/admin/users

OOP Demo
  GET    /api/oop-demo/polymorphism   live runtime-polymorphism trace
```

---

## 8. The OOP Concepts, Explained

**Problem statement:** extend the system using inheritance hierarchies
(MobileGadget, LaptopGadget, etc.) to represent gadget categories, with
method overriding, runtime polymorphism and dynamic binding providing
category-specific behaviour.

### Inheritance
`Gadget` is the abstract superclass (`model/Gadget.java`). `MobileGadget`,
`LaptopGadget`, `SmartwatchGadget` and `AccessoryGadget` all `extends Gadget`,
inheriting its common fields (`name`, `brand`, `price`, `stock`, etc.) and
adding their own category-specific fields.

### Method Overriding
Every subclass overrides `displayDetails()`, `getGadgetType()` and
`getSpecifications()` with its own implementation — see e.g.
`model/MobileGadget.java` vs `model/LaptopGadget.java`.

### Runtime Polymorphism + Dynamic Binding
`service/PolymorphismDemoService.java` declares a single `Gadget gadget`
reference and reassigns it to a `MobileGadget`, then a `LaptopGadget`, then a
`SmartwatchGadget`, then an `AccessoryGadget` — calling
`gadget.displayDetails()` each time. The exact same line of code produces
four different outputs because the JVM resolves the method call using the
object's **actual runtime type**, not the reference's compile-time type
(`Gadget`). This is exposed live at `GET /api/oop-demo/polymorphism` and
rendered on the **OOP Concepts** page in the frontend, so you can run it
during your viva and show the real server response.

`service/GadgetService.java` also uses this hierarchy as a **factory**:
`createFromRequest()` picks the correct subclass to instantiate based on a
`gadgetType` string from the admin "Add Gadget" form, then every downstream
operation (save, update, delete, filter, sort) works uniformly through the
`Gadget` reference type — a second, everyday demonstration of polymorphism
beyond the explicit demo endpoint.

---

## 9. Important Files for Your Viva

| Concept | File |
|---|---|
| Base class | `backend/.../model/Gadget.java` |
| Subclasses | `backend/.../model/MobileGadget.java`, `LaptopGadget.java`, `SmartwatchGadget.java`, `AccessoryGadget.java` |
| Live polymorphism demo | `backend/.../service/PolymorphismDemoService.java` |
| Polymorphic factory | `backend/.../service/GadgetService.java` (`createFromRequest`, `buildSubclassInstance`) |
| JPA inheritance mapping | `@Inheritance(strategy = InheritanceType.JOINED)` in `Gadget.java` |
| Security / JWT | `backend/.../security/`, `config/SecurityConfig.java` |
| Seed data | `backend/.../config/DataSeeder.java` |
| Frontend OOP explainer page | `frontend/src/pages/OopConcepts.jsx` |

---

## 10. Limitations / Demo-Only Parts

Be upfront about these if asked in your viva:

- **Payment is simulated.** Checkout offers "Cash on Delivery" and "Demo Card
  Payment" only — no real payment gateway is integrated, as instructed.
- **Product images** are placeholder images generated via `placehold.co`
  (colored blocks with text labels), not real product photography.
- **Deal countdown timer** on the homepage is frontend-only cosmetic UI (resets
  to the next midnight) — it does not drive any backend deal-expiry logic.
- **Wishlist** is stored in the browser's `localStorage`, not the database —
  it's per-browser, not per-account. (Cart and Orders *are* fully persisted
  server-side per user, as required.)
- **Search autocomplete** calls the real search endpoint (not a static list),
  but is debounced client-side for responsiveness.
- **Recommendations** ("Recommended for You") use simple category-based logic
  driven by the last product type viewed (stored in `localStorage`), exactly
  as scoped in the requirements — no ML/AI involved.
- Product thumbnail gallery on the detail page currently repeats the single
  main image four times (no multi-image upload support) — clearly a
  placeholder for a future enhancement, not a functional gap in core flows.

Everything else described in the spec — auth, CRUD, cart, checkout with
stock decrement, orders, reviews with live rating recalculation, admin
dashboard stats, search/filter/sort, and the full OOP hierarchy — is
implemented with real logic end-to-end, not mocked.

---

## 11. A Note on How This Was Built

This project was generated in a sandboxed environment with **no internet
access**, so `mvn` / `npm install` / a live MySQL instance could not be run
or verified here. Every file was hand-written carefully against the Spring
Boot 3 / Spring Security 6 / React 18 / Vite APIs, but you should still:

1. Run `mvn clean install` and fix any compile errors before your demo.
2. Run `npm install && npm run dev` and click through every page once.
3. Double-check the JWT secret and DB credentials in
   `backend/src/main/resources/application.properties` before submitting.

If something doesn't compile, the error message will point you to the exact
file/line — the architecture and logic are sound, but a syntax slip in
unverified code is possible and worth a final pass.
