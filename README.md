# CampusConnect — Food Delivery & Career Portal

A full-stack academic project that combines the syllabus's **Food Delivery System** and **Job Portal** into one coherent responsive application. It demonstrates frontend design, React state, Bootstrap components, form validation, REST APIs, database CRUD, JWT authentication, cart/order processing, job applications, API testing, Git workflow and deployment.

## 1. Technology stack

| Layer | Technologies |
|---|---|
| User interface | HTML5, CSS3, JavaScript (ES6+), React 18, Bootstrap 5 |
| Frontend tooling | Vite, Fetch API, responsive CSS, reusable React components |
| Backend | Node.js, Express.js, RESTful APIs, middleware |
| Database | MongoDB with Mongoose schemas and relationships |
| Authentication | JWT bearer tokens, bcrypt password hashing, role-based authorization |
| Validation and safety | express-validator, server-side validation, Helmet, CORS, JSON error responses |
| API verification | Postman collection and Node's built-in test runner |
| Version control and deployment | Git/GitHub-ready structure; deployment guide included |

## 2. Project modules

### Food delivery
- Responsive restaurant directory and food menu built with Bootstrap grid/card patterns.
- Responsive navigation with search plus Restaurants, Menu, Offers, Cart and Contact sections.
- Offers carousel (demo promotions) and validated contact form with dynamic feedback.
- Search restaurants, dishes, cuisine and descriptions.
- Filter dishes by Veg / Non-Veg / Vegan, cuisine and maximum price.
- Bootstrap-style food cards and an item-details dialog.
- Authenticated cart with add, increase, decrease and remove operations.
- Live item count and total price; checkout creates an order and clears the cart.
- User order history and admin order-status workflow.

### Job portal
- Register and log in as a student/customer or employer/recruiter.
- Search jobs by role, company, skill and location; filter by job type.
- Employers can create, edit and remove their own job listings.
- Applicants can apply once to each active role.
- Dashboard tracks submitted applications and application status.
- Employers can view applications for jobs they posted and update candidate status.

### Admin workspace
- Create, edit and delete restaurants and menu items through the UI/API.
- Publish and maintain job listings.
- Review orders and update order status.
- Review applications and update candidate status.

## 3. Requirements

- Node.js 20 or later and npm.
- MongoDB Community Server running locally **or** a MongoDB Atlas connection string.
- A modern browser.
- Optional: Postman, Git and GitHub account.

## 4. Run locally on Windows

Open two terminal windows in the `campusconnect` folder.

### Terminal 1 — backend

```powershell
cd server
Copy-Item .env.example .env
```

Open `server/.env` and confirm `MONGODB_URI`. For a local MongoDB server, the default is:

```env
MONGODB_URI=mongodb://127.0.0.1:27017/campusconnect
```

Change `JWT_SECRET` to a long random secret before using the app beyond a local demonstration. Then run:

```powershell
npm install
npm run seed
npm run dev
```

The API starts at `http://localhost:5000`. Check `http://localhost:5000/api/health` for a health response.

### Terminal 2 — frontend

```powershell
cd client
Copy-Item .env.example .env
npm install
npm run dev
```

Open the local URL printed by Vite, normally `http://localhost:5173`.

> **Important:** MongoDB must be running and the backend must stay open. The frontend and backend use separate terminal windows. If MongoDB is in Atlas, place the Atlas connection string in `server/.env` and allow your development IP in Atlas Network Access.

## 5. Demo data and accounts

`npm run seed` creates example restaurants, menu items and sample jobs, plus an administrator account if one does not already exist.

Default local demo admin:

- Email: `admin@campusconnect.local`
- Password: `Admin@12345`

The initial admin credentials come from `server/.env` (`ADMIN_EMAIL` and `ADMIN_PASSWORD`) or the values shown in `.env.example`. Change the default password and JWT secret for any shared/demo deployment. Normal users cannot self-register as administrators; the public registration endpoint only accepts the `user` and `employer` roles.

To try both account types, register one **Student / Customer** account for food ordering and job applications, and one **Employer / Recruiter** account to publish jobs.

## 6. Project structure

```text
campusconnect/
├── README.md
├── PROJECT_REPORT.md
├── .gitignore
├── client/
│   ├── .env.example
│   ├── index.html
│   ├── package.json
│   └── src/
│       ├── main.jsx
│       ├── App.jsx
│       ├── api.js
│       └── styles.css
└── server/
    ├── .env.example
    ├── package.json
    ├── postman/CampusConnect.postman_collection.json
    ├── test/smoke.test.js
    └── src/
        ├── app.js
        ├── server.js
        ├── seed.js
        ├── config/db.js
        ├── middleware/{auth.js,errors.js}
        ├── models/{User,Restaurant,MenuItem,Cart,Order,Job,Application}.js
        ├── routes/{auth,restaurants,menu,cart,orders,jobs,applications}.js
        └── utils/asyncHandler.js
```

## 7. REST API reference

Base URL: `http://localhost:5000/api`

Authenticated endpoints require `Authorization: Bearer <token>`. Obtain a token from the login or registration endpoint. JSON request bodies should use `Content-Type: application/json`.

| Method | Endpoint | Access | Purpose |
|---|---|---|---|
| GET | `/health` | Public | Service health check |
| POST | `/auth/register` | Public | Create user or employer account |
| POST | `/auth/login` | Public | Authenticate and receive JWT |
| GET | `/auth/me` | Login required | Get current profile |
| PATCH | `/auth/profile` | Login required | Update name, phone or bio |
| GET | `/restaurants?search=` | Public | Search restaurants |
| GET | `/restaurants/:id` | Public | Get restaurant with available menu |
| POST / PUT / DELETE | `/restaurants` or `/restaurants/:id` | Admin | Restaurant CRUD |
| GET | `/menu?search=&category=&cuisine=&maxPrice=` | Public | Menu search and filters |
| POST / PUT / DELETE | `/menu` or `/menu/:id` | Admin | Menu-item CRUD |
| GET | `/cart` | Login required | View cart and computed total |
| POST | `/cart/items` | Login required | Add an item (`menuItemId`, `quantity`) |
| PATCH / DELETE | `/cart/items/:menuItemId` | Login required | Update or remove an item |
| DELETE | `/cart` | Login required | Clear cart |
| POST | `/orders` | Login required | Checkout (`deliveryAddress`) |
| GET | `/orders/mine` | Login required | Current user's order history |
| GET | `/orders` | Admin | View all orders |
| PATCH | `/orders/:id/status` | Admin | Update fulfilment status |
| GET | `/jobs?search=&type=&location=` | Public | Search/filter active jobs |
| GET | `/jobs/:id` | Public | Read job details |
| POST | `/jobs` | Employer/Admin | Create a job |
| PUT / DELETE | `/jobs/:id` | Owner/Admin | Edit or deactivate a job |
| POST | `/jobs/:id/apply` | Login required | Apply once to an active job |
| GET | `/applications/me` | Login required | View own applications |
| GET | `/applications` | Employer/Admin | View applications for jobs they can manage |
| PATCH | `/applications/:id/status` | Employer/Admin | Update candidate status |

### Example request bodies

Create account:

```json
{
  "name": "Demo Student",
  "email": "student@example.com",
  "password": "SecurePass123",
  "role": "user"
}
```

Add a menu item (admin token required; use an existing restaurant ObjectId):

```json
{
  "restaurant": "REPLACE_WITH_RESTAURANT_ID",
  "name": "Veggie Wrap",
  "description": "Fresh vegetables with a mint yoghurt dressing.",
  "price": 149,
  "category": "Veg",
  "cuisine": "Healthy",
  "tags": ["Fresh", "Quick bite"]
}
```

Place an order:

```json
{
  "deliveryAddress": "Hostel B, Room 204, University Road, Chandigarh"
}
```

Create a job (employer/admin token required):

```json
{
  "title": "React Developer Intern",
  "company": "Example Studio",
  "location": "Mohali / Hybrid",
  "type": "Internship",
  "salary": "₹15,000 per month",
  "description": "Work with React components, responsive UI, REST APIs and code reviews.",
  "skills": ["React", "JavaScript", "Bootstrap"]
}
```

## 8. API testing with Postman

Import `server/postman/CampusConnect.postman_collection.json` in Postman. The collection contains health, authentication, restaurant/menu, cart/order and job-application examples. Set collection variables for `baseUrl`, IDs and credentials. The collection includes student and employer registration requests and role-aware token capture. For protected calls, use the stored `userToken`, `employerToken` or `adminToken`. Admin endpoints require the admin token; menu/restaurant CRUD does not work with an ordinary student token. Use a fresh email if re-running account-creation requests against the same database.

Run the server tests with:

```powershell
cd server
npm test
```

These smoke tests verify that the health endpoint responds and unknown routes return a JSON 404. For a full workflow check, use the Postman collection with the seeded database.

## 9. Syllabus mapping

| Syllabus experiment | Covered in this project |
|---|---|
| Experiment 1 — Food delivery frontend | Bootstrap grid homepage; responsive navigation/search; Restaurants, Menu, Offers carousel, Cart and Contact; cards and item modal; food/cuisine/price filters; dynamic cart and totals |
| Experiment 2 — Job registration/dashboard | Register/login UI; reusable React UI pieces; controlled forms and browser validation; state with `useState`; dynamic job cards, filters and feedback |
| Experiment 3 — Food-delivery backend | Express REST API; Restaurant/MenuItem/Cart models; CRUD; filtering via API query parameters; cart operations; validation and HTTP status codes |
| Experiment 4 — Job portal backend | User, Job and Application schemas; registration/login; JWT middleware; database integration; search/filter; server-side validation and role permissions |
| Experiment 5 — Food full-stack integration | React fetch client connects to APIs; restaurant/menu data from MongoDB; backend cart/checkout; async request and error-handling flow |
| Experiment 6 — Job full-stack integration | Protected user actions; registration → login → apply → application tracking; employer workflow; Postman tests; Git-ready repository and deployment notes |

**Learning note:** The provided detailed experiments are based on HTML/CSS/Bootstrap/JavaScript, React, Node.js/Express and MongoDB/MySQL. This project uses MongoDB. If your lab specifically requires MySQL, the schema/queries and connection layer need a MySQL adapter rather than only changing the connection string.

## 10. Git workflow

From the project root:

```powershell
git init
git add .
git commit -m "Build CampusConnect full-stack portal"
git branch -M main
# Create an empty GitHub repository, then add its URL:
git remote add origin YOUR_GITHUB_REPOSITORY_URL
git push -u origin main
```

Do not commit `.env`, secrets or production credentials. The `.gitignore` already excludes `.env`, `node_modules` and build output.

## 11. Deployment outline

1. Create a MongoDB Atlas database and load the demo data by running the seed script against that database, or create records using the admin UI.
2. Deploy `server` to Render, Railway or a similar Node hosting service. Set `MONGODB_URI`, a long random `JWT_SECRET`, `JWT_EXPIRES_IN`, `CLIENT_ORIGIN` and `PORT` in the hosting dashboard. The start command is `npm start` from the `server` directory.
3. Deploy `client` to Vercel or Netlify. Set `VITE_API_URL` to the deployed API base URL, including `/api`, then build with `npm run build` from `client`.
4. Set `CLIENT_ORIGIN` on the API to the exact public frontend origin. Redeploy after changing environment variables.
5. Change the default admin password and secret, verify `/api/health`, then test signup, login, menu loading, cart checkout and job application in the deployed environment.

## 12. Current academic-project limitations

- Checkout is a demonstration workflow; no real payment gateway is connected.
- Food delivery is recorded as an order but does not dispatch a delivery partner.
- Image URLs in seeded data require internet access; the UI shows an emoji fallback when an image fails.
- Email verification, password reset, file uploads, resume upload and production rate limiting are not implemented.
- Do not use the sample admin credentials or the default JWT secret for a public/production deployment.
