# 🏠 Buyer Portal — Real Estate Platform

A full-stack real estate buyer portal built with **Ruby on Rails** (API) and **React** (frontend).
Buyers can register, log in, browse properties, and manage their favourites.

---

## Repositories

- **Backend** (Ruby on Rails): [buyer-portal-backend](https://github.com/bishesh-ganesh-shrestha/Buyer-Portal-Backend)
- **Frontend** (React): [buyer-portal-frontend](https://github.com/bishesh-ganesh-shrestha/Buyer-Portal-Frontend)

---

## Tech Stack

**Backend**
- Ruby on Rails 8.1.2 (API-only mode)
- PostgreSQL 16.13
- JWT Authentication
- bcrypt (password hashing)
- RSpec + FactoryBot + Shoulda Matchers (testing)

**Frontend**
- React 18 (Vite)
- React Router DOM (client-side routing)
- Axios (HTTP client)
- CSS (component-scoped stylesheets)

---

## Project Structure

**Backend** (`buyer-portal-backend`)
```
buyer-portal-backend/
├── app/
│   ├── controllers/api/v1/
│   │   ├── auth_controller.rb
│   │   ├── properties_controller.rb
│   │   └── favourites_controller.rb
│   └── models/
│       ├── user.rb
│       ├── property.rb
│       └── favourite.rb
├── spec/
│   ├── models/
│   │   ├── user_spec.rb
│   │   ├── property_spec.rb
│   │   └── favourite_spec.rb
│   ├── requests/
│   │   ├── auth_spec.rb
│   │   ├── properties_spec.rb
│   │   └── favourites_spec.rb
│   └── factories/
│       ├── users.rb
│       ├── properties.rb
│       └── favourites.rb
└── db/
    └── seeds.rb
```

**Frontend** (`buyer-portal-frontend`)
```
buyer-portal-frontend/
└── src/
    ├── context/
    │   └── AuthContext.jsx
    ├── pages/
    │   ├── Login.jsx
    │   ├── Dashboard.jsx
    │   └── PropertyDetail.jsx
    └── components/
        ├── Navbar.jsx
        └── PropertyCard.jsx
```

---

## Database Schema
```
users
  - id
  - name         string
  - email        string (unique)
  - password_digest string
  - role         string (buyer | admin)
  - created_at

properties
  - id
  - title        string
  - address      string
  - price        integer
  - bedrooms     integer
  - bathrooms    integer
  - area_sqft    integer
  - property_type string
  - description  text
  - image_url    string
  - created_at

favourites
  - id
  - user_id      integer (FK → users)
  - property_id  integer (FK → properties)
  - created_at
  - UNIQUE(user_id, property_id)
```

---

## Getting Started

### Prerequisites

Make sure you have these installed:

- Ruby 3.4.9
- Rails 8.1.2
- PostgreSQL 16.13
- Node.js 24.14.0
- npm 11.9.0

No global Vite installation needed — it is included as a local dependency in the frontend project and runs via `npm run dev`.

---

### 1. Backend Setup
```bash
git clone https://github.com/bishesh-ganesh-shrestha/Buyer-Portal-Backend.git
cd Buyer-Portal-Backend
bundle install
```

Set up the database:
```bash
rails db:create
rails db:migrate
rails db:seed
```

Start the Rails server:
```bash
rails server
```

API will be running at `http://localhost:3000`

---

### 2. Frontend Setup

Open a new terminal:
```bash
git clone https://github.com/bishesh-ganesh-shrestha/Buyer-Portal-Frontend.git
cd Buyer-Portal-Frontend
npm install
npm run dev
```

App will be running at `http://localhost:5173`

---

### 3. Open the app

Visit `http://localhost:5173/login` in your browser.
Both servers must be running at the same time.

---

## Example Flows

### Sign Up → Login → Browse → Favourite

**1. Register a new account**
- Go to `http://localhost:5173/login`
- Click **Register** at the bottom
- Enter your full name (first and last name required), email and password
- You are automatically logged in and redirected to the dashboard

**2. Browse properties**
- All available properties are listed on the dashboard
- Click any property card to view full details

**3. Add a favourite**
- Click the ♥ icon on any property card
- Or open a property detail page and click the ♥ icon next to the title
- The heart turns red when favourited

**4. View your favourites**
- Click **My Favourites** tab on the dashboard
- Only your favourited properties are shown
- The tab persists on page refresh via URL query params (`?tab=favourites`)

**5. Remove a favourite**
- Click the ♥ icon again on any favourited property
- It is immediately removed from your favourites

**6. Logout**
- Click **Logout** in the top right navbar
- You are redirected to the login page
- Protected routes redirect unauthenticated users to login automatically

---

## API Endpoints

### Auth
```
POST /api/v1/register   — Register a new user
POST /api/v1/login      — Login and receive JWT token
```

### Properties (requires Authorization header)
```
GET /api/v1/properties        — List all properties
GET /api/v1/properties/:id    — Get single property
```

### Favourites (requires Authorization header)
```
GET    /api/v1/favourites      — List current user's favourites
POST   /api/v1/favourites      — Add a property to favourites
DELETE /api/v1/favourites/:id  — Remove a property from favourites
```

### Authentication Header
All protected endpoints require:
```
Authorization: Bearer <jwt_token>
```

---

## Security

- Passwords are hashed using **bcrypt** — raw passwords are never stored
- Authentication uses **JWT tokens** with 24 hour expiry
- All favourite operations are **scoped to the current user** — users cannot access or modify other users' favourites
- Strong parameters prevent mass assignment attacks
- Login errors use a generic message to prevent user enumeration attacks
- CORS is configured to only allow requests from the frontend origin

---

## Running Tests

The backend has full RSpec test coverage across models and API endpoints.
```bash
cd Buyer-Portal-Backend
bundle exec rspec
```

### Test Coverage

**Model specs** (`spec/models/`)
- User — validations, associations, bcrypt hashing, email downcasing
- Property — validations, associations
- Favourite — associations, uniqueness constraint, user scoping

**Request specs** (`spec/requests/`)
- Auth — register, login, validation errors, security
- Properties — list, show, favourited flag, authentication protection
- Favourites — list, create, delete, user scoping, duplicate prevention
```bash
# Run all specs
bundle exec rspec

# Run only model specs
bundle exec rspec spec/models

# Run only request specs
bundle exec rspec spec/requests

# Run a specific file
bundle exec rspec spec/requests/auth_spec.rb
```

Expected output:
```
63 examples, 0 failures
```

---

## Seed Data

The seed file creates sample data for development:
```bash
rails db:seed
```

This creates:
- 2 users (buyer and admin)
- 5 properties across Kathmandu and Lalitpur

**Sample login credentials:**
```
Email:    abc@gmail.com
Password: password
```

---

## Design Decisions

**Why bcrypt + JWT instead of Devise?**
Devise is designed for full Rails apps with views. For an API-only app, raw bcrypt + JWT is simpler, more transparent, and easier to reason about. It also demonstrates a clear understanding of the auth flow.

**Why a `role` string column instead of a separate Role model?**
With only two roles (buyer and admin), a separate roles table would be over-engineering. The string column with inclusion validation is clean and simple. In a production system with complex permissions this could be extracted into a roles table with a many-to-many relationship.

**Why URL query params for tab state?**
Storing the active tab in the URL (`?tab=favourites`) means the URL always reflects what the user is seeing. This makes the page refreshable, bookmarkable, and shareable — consistent with how real web applications work.

**Why scope all favourite queries to current_user?**
Rather than finding a favourite by its own ID and then checking ownership, we always query through `current_user.favourites`. This makes it architecturally impossible to accidentally expose another user's data.