# Backend Requirement Specifications – Airbnb Clone

This document outlines the technical and functional requirements for three key backend features of the Airbnb Clone:

1. User Authentication  
2. Property Management  
3. Booking System

---

## 1. User Authentication

### Functional Requirements
- Allow users to register as either a guest or a host.
- Enable users to log in securely using email and password.
- Implement JWT-based session management.
- Allow OAuth integration (Google, Facebook) in future.

### API Endpoints

POST /api/auth/register  
- Input:
  {
    "first_name": "John",
    "last_name": "Doe",
    "email": "john@example.com",
    "password": "securePass123",
    "role": "guest"
  }

- Validation:
  - Email must be unique and valid.
  - Password must meet minimum strength criteria.
  - Role must be either "guest" or "host".

- Output:
  {
    "message": "User registered successfully",
    "user_id": "uuid"
  }

POST /api/auth/login  
- Input:
  {
    "email": "john@example.com",
    "password": "securePass123"
  }

- Output:
  {
    "token": "JWT",
    "user": {
      "user_id": "uuid",
      "role": "guest"
    }
  }

### Performance & Security
- JWT token should expire after 24 hours.
- Use bcrypt hashing for passwords.
- Rate-limit login attempts to prevent brute-force attacks.

---

## 2. Property Management

### Functional Requirements
- Allow hosts to create, update, delete, and view their listings.
- Each property includes details like title, description, price, availability, and location.

### API Endpoints

POST /api/properties  
- Input:
  {
    "name": "Cozy Apartment",
    "description": "A nice spot in the city",
    "location": "Lusaka, Zambia",
    "pricepernight": 75.00
  }

- Validation:
  - All fields are required.
  - pricepernight must be greater than 0.

- Output:
  {
    "property_id": "uuid",
    "message": "Property created successfully"
  }

GET /api/properties/:id  
- Output:
  {
    "property_id": "uuid",
    "host_id": "uuid",
    "name": "Cozy Apartment",
    "description": "...",
    "location": "Lusaka",
    "pricepernight": 75.00
  }

### Performance & Security
- Cache results of popular properties using Redis.
- Only hosts can modify their own listings (RBAC).

---

## 3. Booking System

### Functional Requirements
- Guests can book available properties.
- Hosts and guests can view and cancel bookings.
- Prevent double-bookings for overlapping dates.

### API Endpoints

POST /api/bookings  
- Input:
  {
    "property_id": "uuid",
    "start_date": "2025-07-01",
    "end_date": "2025-07-05"
  }

- Validation:
  - Dates must not overlap existing confirmed bookings.
  - Start date must be before end date.
  - Booking must be at least one night.

- Output:
  {
    "booking_id": "uuid",
    "total_price": 300.00,
    "status": "pending"
  }

PATCH /api/bookings/:id/cancel  
- Input:
  {
    "reason": "Change of plans"
  }

- Output:
  {
    "message": "Booking cancelled"
  }

### Performance & Security
- Validate bookings with atomic transactions.
- Limit booking attempts per user to avoid spam.
- Use background jobs to send booking confirmations via email.

---

## Summary

Feature              | Endpoint(s)                        | Auth Required | Notes
---------------------|-------------------------------------|----------------|-----------------------------
User Authentication | /api/auth/register, /login         | No / Yes       | JWT-based, secure password
Property Management | /api/properties, /properties/:id   | Yes            | Hosts manage their properties
Booking System      | /api/bookings, /bookings/:id       | Yes            | Prevents date conflicts

