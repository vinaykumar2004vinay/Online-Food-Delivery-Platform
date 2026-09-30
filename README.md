# 🍔 Food Delivery App (React + Spring Boot)

A full-stack online food ordering app. Customers can browse restaurants, add dishes to a cart, and pay online. Restaurant owners get their own dashboard to manage menus and orders. An admin can look after the whole platform.

---

## What can you do with it?

**As a customer**
- Sign up and log in (secured with JWT tokens)
- Reset your password through an email link
- Browse restaurants and open a restaurant to see its menu
- Search for restaurants or dishes
- Mark restaurants as favourites
- Add food to your cart, change quantities, and pick your ingredients
- Save delivery addresses
- Pay online with **Razorpay** (Stripe support is also in the code)
- See your past orders and get notifications
- Check out restaurant events

**As a restaurant owner**
- Create and edit your restaurant (details, opening hours, images)
- Add menu items, set categories and ingredients, and mark items in or out of stock
- See incoming orders and update their status: *Received → Pending → Ready for pickup → Out for delivery → Delivered* (or *Cancelled*)
- Create events for your restaurant

**As an admin**
- View all customers and restaurants
- Review restaurant requests waiting for approval

---

## Built with

| Part | Technology |
|------|-----------|
| Frontend | React 18, Redux + Redux Thunk, React Router, Material UI, Tailwind CSS, Formik + Yup, Axios, React Slick |
| Backend | Java 17, Spring Boot 3.1, Spring Security, Spring Data JPA, JWT (jjwt), Spring Mail |
| Database | MySQL |
| Payments | Razorpay, Stripe |
| Image upload | Cloudinary (from the frontend) |
| API testing | Postman collection included |

---

## Project structure

```
Source/
├── backend-spring boot/     # Spring Boot REST API (runs on port 5454)
│   └── src/main/java/com/zosh/
│       ├── controller/      # API endpoints
│       ├── service/         # Business logic (orders, cart, payments...)
│       ├── repository/      # Database access
│       ├── model/           # Entities: User, Restaurant, Food, Order, Cart...
│       ├── config/          # Security + JWT setup
│       └── request/ response/ dto/ Exception/ domain/
├── frontend-react/          # React app (runs on port 3000)
│   └── src/
│       ├── customers/       # Customer pages and components
│       ├── Admin/           # Restaurant owner dashboard
│       ├── SuperAdmin/      # Platform admin screens
│       ├── State/           # Redux actions and reducers
│       └── config/api.js    # Backend URL
└── Zosh Food.postman_collection.json
```

---

## Getting started

### What you need first
- Java 17
- Maven (or use the included `mvnw`)
- Node.js and npm
- MySQL running locally
- Accounts/keys for: Razorpay (and/or Stripe), Gmail app password (for reset-password emails), Cloudinary

### 1. Backend

1. Open `backend-spring boot` in your IDE (IntelliJ, Eclipse, VS Code...).
2. Create an empty MySQL database, for example `zosh_food`.
3. Open `src/main/resources/application.properties` and fill in your own details:

   ```properties
   # Database (or set the DB_HOST, DB_PORT, DB_NAME, DB_USERNAME, DB_PASSWORD environment variables)
   spring.datasource.username=root
   spring.datasource.password=YOUR_DB_PASSWORD

   # Razorpay
   razorpay.api.key=YOUR_RAZORPAY_KEY
   razorpay.api.secret=YOUR_RAZORPAY_SECRET

   # Stripe (optional)
   stripe.api.key=YOUR_STRIPE_SECRET_KEY

   # Email for password reset
   spring.mail.username=YOUR_EMAIL
   spring.mail.password=YOUR_GMAIL_APP_PASSWORD
   ```

   Tables are created automatically the first time you run the app.
4. Run it:

   ```bash
   cd "backend-spring boot"
   ./mvnw spring-boot:run
   ```

   The API will be live at `http://localhost:5454`.

### 2. Frontend

```bash
cd frontend-react
npm install
npm start
```

The app opens at `http://localhost:3000`.

If your backend runs somewhere else, change the URL in `src/config/api.js`.

You'll also need to point the image upload to your own Cloudinary account in `src/Admin/utils/UploadToCloudnary.js`.

---

## How the pieces talk to each other

- The React app calls the backend at `http://localhost:5454`.
- After login, the backend returns a JWT which the frontend sends with every request.
- `/api/admin/**` is only for restaurant owners and admins. Other `/api/**` routes need a logged-in user.
- When a customer places an order, the backend creates a Razorpay payment link. After paying, the user lands back on `/payment/success/:orderId`.

### Some of the API routes

| Area | Examples |
|------|----------|
| Auth | `POST /auth/signup`, `POST /auth/signin`, `POST /auth/reset-password-request`, `POST /auth/reset-password` |
| Restaurants | `GET /api/restaurants`, `GET /api/restaurants/search`, `PUT /api/restaurants/{id}/add-favorites` |
| Food | `GET /api/food/restaurant/{id}`, `GET /api/food/search` |
| Cart | `PUT /api/cart/add`, `PUT /api/cart-item/update`, `DELETE /api/cart-item/{id}/remove`, `GET /api/cart/total` |
| Orders | `POST /api/order`, `GET /api/order/user` |
| Owner/Admin | `/api/admin/restaurants`, `/api/admin/food`, `/api/admin/ingredients`, `/api/admin/category`, `/api/admin/events/restaurant/{id}`, `/api/admin/order/restaurant/{id}` |

The full list is in the included Postman collection (`Zosh Food.postman_collection.json`). Import it into Postman to try everything out.

---

## User roles

- `ROLE_CUSTOMER` – the default for anyone who signs up
- `ROLE_RESTAURANT_OWNER` – manages a restaurant
- `ROLE_RESTAURANT_MANAGER`
- `ROLE_ADMIN` – manages the platform

---

## Things to know before you deploy

- **Never commit real keys.** Keep Razorpay, Stripe, email and database passwords in environment variables or a local file that is in `.gitignore`.
- The Razorpay callback URL is currently `http://localhost:3000/payment/success/...`. Change it to your real domain when you go live.
- CORS and the API URL are set for local development. Update them for production.

---

## What I learned

Working on this taught me how a real full-stack app fits together: role-based security with JWT, Redux state management, connecting a payment gateway, and building separate experiences for customers and restaurant owners.

---
