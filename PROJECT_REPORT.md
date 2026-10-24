# CampusConnect: Food Delivery and Career Portal

## Abstract

CampusConnect is a responsive full-stack web application that combines a food delivery experience and a job portal in one platform. Users can browse restaurants and food items, search and filter the menu, manage a shopping cart and place demo orders. They can also search job listings, submit applications and track application statuses. Employers publish job listings and review applications, while administrators manage restaurants, menu items and order status. The application uses React and Bootstrap on the client side, Node.js and Express for RESTful services, MongoDB for persistent data, and JSON Web Tokens (JWT) for authenticated requests.

## 1. Introduction

Students often use separate services to organise everyday food needs and explore early-career opportunities. CampusConnect demonstrates how these experiences can be implemented using a common account, responsive interface and reusable full-stack architecture. This project brings together the practical syllabus concepts from the food-delivery and job-portal experiments: HTML, CSS, JavaScript, Bootstrap, React components, state management, REST APIs, database CRUD, authentication, testing and deployment.

## 2. Problem statement

Design and implement a responsive, interactive, database-backed web platform that provides food discovery and ordering features alongside a job-search and application workflow. The system must support dynamic data, search and filtering, user authentication, protected operations, server-side validation, clear feedback messages and a maintainable API structure.

## 3. Objectives

1. Build a mobile-friendly UI using React, Bootstrap 5, HTML, CSS and JavaScript.
2. Create reusable UI components and manage changing UI data with React state and effects.
3. Provide REST endpoints for restaurants, menu items, carts, orders, users, jobs and applications.
4. Persist data in MongoDB through validated Mongoose models.
5. Secure account passwords with hashing and protect private endpoints using JWT middleware and role checks.
6. Implement search, filtering, CRUD, cart totals, checkout and application tracking.
7. Verify key API behaviours and provide setup, version-control and deployment instructions.

## 4. Scope

### Included
- Responsive homepage and navigation.
- Responsive restaurant and menu browsing with Bootstrap grid/card patterns, search/filter controls, Offers carousel and Contact form.
- Food item detail dialog and dynamic cart.
- Demo order creation and order history.
- Registration, login, profile name update and role-based access.
- Job listing search/filter, employer job management and one-application-per-user-per-job enforcement.
- Dashboard for orders and submitted job applications.
- Admin management view for restaurants, menu items, orders and applications.
- REST API validation, consistent errors, Postman collection and smoke tests.

### Not included in this academic version
- Real payment processing.
- Delivery partner tracking or live maps.
- Email verification, password recovery or resume uploads.
- Production monitoring, automated backups and rate limiting.

## 5. Software and hardware requirements

**Software:** Node.js 20+, npm, MongoDB Community or MongoDB Atlas, modern web browser, code editor, optional Postman and Git.

**Hardware:** A typical laptop/desktop with 4 GB or more RAM and an internet connection for remote images or MongoDB Atlas. A local database can be used without an internet connection.

## 6. Technology overview

| Technology | Purpose in the project |
|---|---|
| HTML5 | Semantic page structure |
| CSS3 | Custom visual system, responsive layouts and animations |
| Bootstrap 5 | Grid, navbar, forms, buttons, cards and responsive utilities |
| JavaScript ES6+ | Data transformations, validation and asynchronous API requests |
| React | Component-based UI and reusable stateful interaction |
| Node.js | JavaScript runtime for backend services |
| Express.js | API routing and middleware |
| MongoDB | Persistent storage for accounts, restaurants, menu, carts, orders, jobs and applications |
| Mongoose | Schema definitions, validation, references and database queries |
| JWT | Signed tokens for authenticated API requests |
| bcryptjs | Password hashing and password comparison |
| Postman / Node test runner | API workflow testing and smoke tests |
| Git | Version history and collaboration |

## 7. System architecture

```text
┌───────────────────────────────────────────────────────────┐
│                      User's Browser                       │
│  React UI · HTML · CSS · Bootstrap · JavaScript / Fetch   │
└──────────────────────────┬────────────────────────────────┘
                           │ HTTPS/JSON + JWT bearer token
                           ▼
┌───────────────────────────────────────────────────────────┐
│                 Node.js + Express API                     │
│  Routes → Validation → JWT/Role Middleware → Controllers  │
│  Error handler · HTTP status codes · Helmet · CORS        │
└──────────────────────────┬────────────────────────────────┘
                           │ Mongoose models and queries
                           ▼
┌───────────────────────────────────────────────────────────┐
│                        MongoDB                            │
│ Users · Restaurants · MenuItems · Carts · Orders           │
│ Jobs · Applications                                        │
└───────────────────────────────────────────────────────────┘
```

The React client uses the Fetch API to communicate with Express endpoints. The backend validates incoming data, verifies user identity where required, applies role restrictions and reads or writes MongoDB documents. Responses are returned as JSON. The UI renders success or error feedback without requiring a complete page reload.

## 8. Main modules

### 8.1 Authentication and profile
- Registration validates name, email and password length.
- Passwords are hashed with bcryptjs before database storage.
- Login verifies the submitted password and signs a JWT.
- The client stores the token locally and sends it in the `Authorization: Bearer` header.
- Middleware loads the current user and optionally restricts actions to administrator or employer roles.

### 8.2 Food discovery and menu filtering
- Restaurant and menu data are loaded from database-backed APIs.
- Search can match restaurant names, cuisine, food names and descriptions.
- Menu query parameters support category, cuisine and maximum price.
- Responsive cards show item images, categories, names and prices; failed images have fallback visuals.

### 8.3 Cart and order workflow
- Each authenticated account has one cart document.
- Cart lines refer to menu items and store quantity.
- Subtotals and total price are calculated from current menu prices.
- Checkout validates the delivery address, snapshots item names/prices into an order, then clears the cart.
- Users see their order history; an admin can update the fulfilment status.

### 8.4 Job search and applications
- Public job listings support search and filters.
- Employer accounts can create and update their own listings; administrators can manage all listings.
- An applicant can submit one application per active job; a unique compound index also protects against duplicate applications.
- Applicants see status in the dashboard; employers see applications related to their own jobs and can progress statuses.

### 8.5 Administration
- Administrators maintain the restaurant directory, food catalogue and job listings.
- Order statuses include Placed, Confirmed, Preparing, Out for delivery, Delivered and Cancelled.
- Application statuses include Submitted, Under review, Shortlisted, Rejected and Hired.

## 9. Data model overview

| Collection | Important fields | Relationship / purpose |
|---|---|---|
| User | name, email, password hash, role, phone, bio | Account and access control |
| Restaurant | name, cuisine, location, rating, deliveryTime | Parent of menu items |
| MenuItem | restaurant, name, price, category, cuisine, isAvailable | Restaurant menu catalogue |
| Cart | user, items [{menuItem, quantity}] | One cart per user |
| Order | user, items snapshot, total, deliveryAddress, status | Immutable snapshot of checkout details plus workflow status |
| Job | title, company, location, type, salary, description, skills, postedBy | Job advertisement and ownership |
| Application | user, job, status, coverNote | Connects applicant to job; unique user+job pair |

## 10. Important workflows

### Registration and login

```text
Enter registration/login details
          ↓
Client form validation
          ↓
POST /api/auth/register or /api/auth/login
          ↓
Express validation + database lookup
          ↓
Hash/compare password and create JWT
          ↓
Return token and public profile
          ↓
Client stores token and requests protected data
```

### Food ordering

```text
Load restaurants/menu → Search/filter items → Add to cart
                                      ↓
                         Update quantities and total
                                      ↓
                       Enter delivery address → Checkout
                                      ↓
                 API creates Order and clears Cart
                                      ↓
                          Show order reference
```

### Job application

```text
Browse/search jobs → Open job board → Apply
                                      ↓
                   Verify session and active job
                                      ↓
              Prevent duplicate application submissions
                                      ↓
                   Save Application in MongoDB
                                      ↓
             Candidate tracks status in dashboard
                                      ↓
             Employer reviews and updates the status
```

## 11. Security and validation

- Passwords are stored as bcrypt hashes rather than plaintext.
- JWTs are signed using a secret provided through environment configuration.
- Role middleware protects administrative and employer-only operations.
- Server-side request validation checks fields even if the client is bypassed.
- Database schemas constrain required fields, enums, length, numeric ranges and relationships.
- A unique index prevents duplicate applications to the same job by the same account.
- Helmet sets common HTTP security headers; CORS is configurable through environment variables.
- A central error handler returns JSON responses with appropriate status codes for validation, missing routes and database conflicts.

For a public deployment, use a strong unique JWT secret, change demo admin credentials, restrict CORS to the deployed frontend, add rate limiting and adopt a secure token strategy appropriate to the deployment.

## 12. Validation and testing plan

| Test case | Expected result |
|---|---|
| GET `/api/health` | HTTP 200 and status `ok` |
| Register with an invalid email or short password | HTTP 400 validation response |
| Register an already-used email | HTTP 409 conflict response |
| Login using wrong credentials | HTTP 401 response |
| Call protected cart without JWT | HTTP 401 response |
| Call admin-only CRUD as a normal user | HTTP 403 response |
| Filter menu by `category=Veg` | Only matching available items returned |
| Add quantity to a cart | Item count and total update correctly |
| Checkout with an empty cart | HTTP 400 response |
| Apply to the same job twice | Second attempt rejected with HTTP 409 |
| Request unknown API route | HTTP 404 JSON response |
| Use responsive layouts on mobile / tablet / desktop | Navigation, cards, forms and management lists remain usable |

The included `server/test/smoke.test.js` validates the health endpoint and JSON 404 handler. The provided Postman collection covers the major API groups. Full workflow testing should be performed with MongoDB running and seeded data available.

## 13. Installation and execution

1. Install Node.js 20+ and start MongoDB locally or create an Atlas database.
2. In `server`, copy `.env.example` to `.env`, configure the database URI and a non-default JWT secret.
3. Run `npm install`, `npm run seed`, and `npm run dev`.
4. In a second terminal, enter `client`, copy `.env.example` to `.env`, then run `npm install` and `npm run dev`.
5. Open the Vite URL and register a student or employer account. For administrator features, sign in using the seeded local demo admin credentials described in README.
6. Import the Postman collection for API tests. Run `npm test` inside `server` for smoke tests.

## 14. Mapping to the syllabus

- **Experiment 1:** Bootstrap responsive grid homepage and navigation/search; Restaurants, Menu, Offers carousel, Cart and Contact sections; food cards and item modal; dietary/cuisine/price filters and dynamic cart UI.
- **Experiment 2:** Registration/login, responsive dashboard, reusable React components, `useState`/`useEffect`, dynamic job listings, validation and dynamic messages.
- **Experiment 3:** Express REST APIs, MongoDB models, CRUD for restaurants/menu/cart, API-side food filtering and error status codes.
- **Experiment 4:** Authentication APIs, user/job/application schema, JWT, protected routes, job search and server validation.
- **Experiment 5:** React Fetch API integration with restaurant/menu endpoints, cart and checkout services, async state updates and visible error handling.
- **Experiment 6:** Registration → login → dashboard → job application, JWT bearer tokens, employer/admin protected actions, API tests, Git workflow and deployment steps.

## 15. Result and conclusion

CampusConnect demonstrates how a modern frontend and backend can be connected through REST APIs and a shared database. It combines data-driven UI rendering, responsive design, authentication, CRUD operations, search and filtering, cart/order management and job application workflows in one academic project. Its modular structure separates UI, API routes, database models and middleware, making the application suitable for practical demonstrations and further enhancement.

## 16. Future enhancements

- Payment gateway integration and delivery tracking.
- Resume upload and profile completion for job seekers.
- Email notification for order and application status changes.
- Pagination, sorting, saved jobs and menu favourites.
- Automated unit and integration tests for each API route.
- MySQL adapter if required by a database-specific lab task.
- Docker setup, CI workflows and production logging/monitoring.

## References

1. React documentation — https://react.dev/
2. Bootstrap documentation — https://getbootstrap.com/docs/5.3/
3. Node.js documentation — https://nodejs.org/docs/latest/api/
4. Express documentation — https://expressjs.com/
5. MongoDB documentation — https://www.mongodb.com/docs/
6. Mongoose documentation — https://mongoosejs.com/docs/
7. JSON Web Token introduction — https://jwt.io/introduction
