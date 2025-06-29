<h2>User Authentication</h2>
🔹 Functional Requirements
Users can register as guests or hosts.

Users can log in using email and password.

JWT tokens will be used for session management.

🔹 API Endpoints
POST /api/auth/register
Input: { first_name, last_name, email, password, role }

Validation:

email: valid and unique

password: min 8 characters

role: must be guest or host

Output: 201 Created, user object (without password), JWT token

Errors: 400 Bad Request, 409 Email already exists

POST /api/auth/login
Input: { email, password }

Validation:

email must exist

password must match hashed value

Output: 200 OK, JWT token

Errors: 401 Unauthorized, 400 Invalid input

🔹 Performance & Security
Passwords are hashed (bcrypt).

Token expiry: 1 hour (refresh token optional).

Rate limiting: 5 attempts/minute/IP.

<h2>Property Management</h2>
🔹 Functional Requirements
Hosts can create, edit, and delete property listings.

Each property includes details like title, description, location, price, and availability.

🔹 API Endpoints
POST /api/properties
Input: { name, description, location, pricepernight, amenities[] }

Auth: Requires host role (JWT)

Validation:

pricepernight: positive number

location: non-empty

Output: 201 Created, property object

Errors: 400 Bad Request, 403 Forbidden

PUT /api/properties/:id
Input: same as above

Auth: Only by property owner (JWT)

Output: 200 OK, updated property object

DELETE /api/properties/:id
Auth: Only by property owner (JWT)

Output: 204 No Content

🔹 Performance & Validation
Max 50 properties per host listed in one fetch.

Query cache enabled for frequent searches (Redis recommended).

<h2>Booking System</h2>
🔹 Functional Requirements
Guests can book available properties.

Bookings prevent overlap using date validation.

Hosts or guests can cancel bookings.

🔹 API Endpoints
POST /api/bookings
Input: { property_id, start_date, end_date }

Validation:

start_date < end_date

No overlapping booking for the same property

Output: 201 Created, booking object with status: pending

Errors: 400 Invalid dates, 409 Conflict (overlap)

PATCH /api/bookings/:id/cancel
Auth: Owner of booking or host of the property

Output: 200 OK, status: canceled

🔹 Performance
Date conflict check done via indexed date range query.

Booking status indexed for fast status queries.
