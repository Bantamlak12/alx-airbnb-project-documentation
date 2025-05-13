# 📚 Airbnb Clone Backend – Technical & Functional Requirements

This document outlines the detailed technical and functional specifications for three core backend features: User Authentication, Property Management, and Booking System.

---

## 🔐 1. User Authentication

### ✅ Functional Requirements
- Users can register as **Guests** or **Hosts**.
- Users can log in using **email/password** or **OAuth** (e.g., Google).
- Authentication is handled via **JWT tokens**.
- Users can manage their profiles after logging in.

### 🌐 API Endpoints

#### `POST /api/auth/register` – Register a new user

**Request Body:**
```json
{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "password": "securePassword123",
  "role": "host"
}
````

**Validation Rules:**

* `email` must be unique and follow a valid email format.
* `password` must be at least 8 characters long.
* `role` must be either `"guest"` or `"host"`.

**Response:**

```json
{
  "message": "Registration successful",
}
```

---

#### `POST /api/auth/login` – Log in an existing user

**Request Body:**

```json
{
  "email": "jane@example.com",
  "password": "securePassword123"
}
```

**Response:**

```json
{
  "message": "Login successful",
  "token": "<jwt_token>"
}
```

### 🚀 Performance Criteria

* Response time should be under **300ms** under normal load.
* Use **bcrypt** for hashing passwords.
* Token generation should be efficient and secure.

---

## 🏡 2. Property Management

### ✅ Functional Requirements

* Hosts can create, update, and delete property listings.
* Each listing includes: title, description, price, location, amenities, images, and availability.

### 🌐 API Endpoints

#### `POST /api/properties` – Create a new property listing

**Request Body:**

```json
{
  "title": "Cozy Apartment",
  "description": "Near downtown, quiet neighborhood.",
  "location": "Lagos, Nigeria",
  "price": 45.00,
  "amenities": ["WiFi", "Kitchen", "Air Conditioning"],
  "availability": {
    "start": "2025-06-01",
    "end": "2025-06-30"
  }
}
```

**Validation Rules:**

* `title` is required, max 100 characters.
* `price` must be a positive decimal number.
* `amenities` must contain at least one item.
* `availability.start` and `availability.end` must be future dates.

**Response:**

```json
{
  "message": "Listing created successfully",
  "property_id": "abc123"
}
```

---

#### `GET /api/properties?location=lagos&minPrice=20&maxPrice=100`

* Returns a filtered and paginated list of property listings.

### 🚀 Performance Criteria

* Must support **pagination** (default 10 listings per page).
* Use **indexed queries** for fast filtering by price and location.
* Properties with high demand should be **cached using Redis**.

---

## 📅 3. Booking System

### ✅ Functional Requirements

* Guests can book available properties for a specific date range.
* System prevents overlapping bookings.
* Guests and hosts can cancel bookings (based on policy).
* Booking statuses include: pending, confirmed, canceled, completed.

### 🌐 API Endpoints

#### `POST /api/bookings` – Create a new booking

**Request Body:**

```json
{
  "property_id": "abc123",
  "start_date": "2025-06-10",
  "end_date": "2025-06-15"
}
```

**Validation Rules:**

* Dates must not overlap with existing bookings.
* `start_date` must be before `end_date`.
* Property must be available during the requested period.

**Response:**

```json
{
  "message": "Booking confirmed",
  "booking_id": "book789",
  "status": "confirmed"
}
```

---

#### `DELETE /api/bookings/:id` – Cancel a booking

* Can be invoked by either guest or host.

**Response:**

```json
{
  "message": "Booking cancelled successfully"
}
```

### 🚀 Performance Criteria

* Must handle **100+ concurrent booking requests per second**.
* Use **date range indexing** to prevent double bookings.
* Emit **notifications** on booking creation or cancellation.
