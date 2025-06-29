# Requirement Specifications for Backend Features

# Backend Feature: Property Booking
* This feature handles the end-to-end process of a guest requesting to book a property, from initial availability checks to final confirmation or rejection by the host.

## 1. API Endpoints
### 1.1. Request Booking
Endpoint: POST /api/bookings

Description: Allows a guest to initiate a booking request for a property.

### 1.2. Host Respond to Booking Request
Endpoint: PUT /api/bookings/{bookingId}/status

Description: Allows a host to approve or reject a pending booking request.

### 1.3. Get Booking Details (Guest/Host)
Endpoint: GET /api/bookings/{bookingId}

Description: Retrieves details of a specific booking.

### 1.4. Get User's Bookings
Endpoint: GET /api/users/{userId}/bookings

Description: Retrieves a list of bookings for a specific user (either guest or host).

## 2. Input/Output Specifications
### 2.1. Request Booking (POST /api/bookings)

### 2.2. Host Respond to Booking Request (PUT /api/bookings/{bookingId}/status)


### 2.3. Get Booking Details (GET /api/bookings/{bookingId})

### 2.4. Get User's Bookings (GET /api/users/{userId}/bookings)
## 3. Validation Rules
* Dates:

    * checkInDate must be in the future.

    * checkOutDate must be after checkInDate.

    * Booking duration should not exceed a predefined maximum (e.g., 365 days).

    * Number of Guests: Must be a positive integer and not exceed the property's maximum guest capacity.

* Property ID: Must be a valid UUID corresponding to an existing property.
  
* User IDs: Must be valid UUIDs corresponding to existing users.

* Host Action:

     * action must be either "APPROVE" or "REJECT".

     * If action is "REJECT", reasonForRejection must be provided and not empty.

     * Host must be the owner of the property associated with the bookingId.

* Booking Status: A host can only respond to a booking if its status is PENDING.

* Authorization:

     * Only authenticated guests can request a booking.

     * Only the authenticated host of the specific property can approve or reject a booking.

     * Guests can only view their own bookings. Hosts can only view bookings for their properties.

## 4. Performance Criteria
* Availability Check & Price Calculation: Should complete within 100ms for 95% of requests.

* Booking Request (initial submission): Should complete within 200ms for 95% of requests (including initial database writes and external service calls for pre-auth).

* Host Response (approve/reject): Should complete within 300ms for 95% of requests (including database updates, payment gateway calls, and notification service calls).

* Concurrency: The system must handle at least 500 concurrent booking requests per second without significant degradation in response times.

* Scalability: The architecture should support horizontal scaling to accommodate growing traffic. Database queries for availability and booking status should be optimized with appropriate indexing.

# Backend Feature: User Authentication
This feature provides secure mechanisms for users to register, log in, and manage their authentication tokens.

## 1. API Endpoints
### 1.1. User Registration
* Endpoint: POST /api/auth/register

* Description: Allows a new user to create an account.

### 1.2. User Login
* Endpoint: POST /api/auth/login

* Description: Allows an existing user to log in and obtain an authentication token.

### 1.3. User Logout
* Endpoint: POST /api/auth/logout

* Description: Invalidates the user's current authentication token.

### 1.4. Refresh Token (Optional but Recommended)
* Endpoint: POST /api/auth/refresh-token

* Description: Allows users to obtain a new access token using a refresh token, without re-entering credentials.

## 2. Input/Output Specifications
### 2.1. User Registration (POST /api/auth/register)
* Input (Request Body - JSON):

### 2.2. User Login (POST /api/auth/login)
* Input (Request Body - JSON):

### 2.3. User Logout (POST /api/auth/logout)
* Input (Headers): Authorization: Bearer <accessToken>

* Input (Request Body - JSON):

### 2.4. Refresh Token (POST /api/auth/refresh-token) - If implemented
* Input (Request Body - JSON):

## 3. Validation Rules
* Email: Must be a valid email format, unique in the system.

* Password:

    * Minimum length: 8 characters.

    * Maximum length: 100 characters.

    * Must contain at least one uppercase letter, one lowercase letter, one number, and one special character (e.g., !@#$%^&*).

    * Passwords should be hashed using a strong, industry-standard algorithm (e.g., bcrypt) before storage.

* First Name/Last Name: Cannot be empty, max 50 characters.

* User Role: Must be GUEST or HOST.

* Authentication Token: JWTs must be signed and validated against a secret key. Expiration must be enforced.

## 4. Performance Criteria
* Registration: Should complete within 150ms for 95% of requests.

* Login: Should complete within 100ms for 95% of requests.

* Logout: Should complete within 50ms for 95% of requests.

* Refresh Token: Should complete within 50ms for 95% of requests.

* Scalability: The authentication service should be highly scalable and stateless (for access tokens) to handle a large number of concurrent login/registration attempts. Database queries for user credentials should be highly optimized.

# Backend Feature: Property Management
* This feature allows hosts to list, update, and manage their properties.

## 1. API Endpoints
### 1.1. Create Property
* Endpoint: POST /api/properties

* Description: Allows a host to create a new property listing.

### 1.2. Get Property Details
* Endpoint: GET /api/properties/{propertyId}

* Description: Retrieves details of a specific property.

### 1.3. Update Property
* Endpoint: PUT /api/properties/{propertyId}

* Description: Allows a host to update an existing property listing.

### 1.4. Delete Property
* Endpoint: DELETE /api/properties/{propertyId}

* Description: Allows a host to remove a property listing.

### 1.5. Get Host's Properties
* Endpoint: GET /api/users/{hostId}/properties

* Description: Retrieves a list of properties owned by a specific host.

### 1.6. Update Property Availability
* Endpoint: PUT /api/properties/{propertyId}/availability

* Description: Allows a host to update specific dates in their property's availability calendar (e.g., block dates).

## 2. Input/Output Specifications
### 2.1. Create Property (POST /api/properties)
* Input (Request Body - JSON):

### 2.2. Get Property Details (GET /api/properties/{propertyId})
* Input: propertyId (path parameter)

* Output (Success - HTTP 200 OK - JSON):

### 2.3. Update Property (PUT /api/properties/{propertyId})
* Input (Request Body - JSON): (Partial updates are supported - send only fields to be updated)

### 2.4. Delete Property (DELETE /api/properties/{propertyId})
* Input (Headers): Authorization: Bearer <accessToken>

* Input (Path Parameter): propertyId

* Output (Success - HTTP 204 No Content): (No response body)

* Output (Failure - HTTP 403 Forbidden / 404 Not Found - JSON):

### 2.5. Get Host's Properties (GET /api/users/{hostId}/properties)
* Input: hostId (path parameter), status (query parameter - ACTIVE, INACTIVE, optional)

* Output (Success - HTTP 200 OK - JSON):

### 2.6. Update Property Availability (PUT /api/properties/{propertyId}/availability)
* Input (Request Body - JSON):

## 3. Validation Rules
* Host Authorization: Only authenticated users with the HOST role can create, update, or delete properties. They can only manage properties they own.

* Required Fields: title, description, address (all sub-fields), propertyType, basePricePerNight, numberOfBedrooms, numberOfBathrooms, maxGuests, images are mandatory for creation.

* Data Types and Ranges:

   * Prices: Positive decimal numbers.

   * Counts (bedrooms, bathrooms, guests): Positive integers or decimals (for bathrooms).

   * title, description: Max length limits enforced.

   * address fields: Valid formats (e.g., zip code patterns).

   * images: Must be valid URLs.

* Availability Update:

    * date must be in YYYY-MM-DD format and in the future.

    * status must be AVAILABLE or BLOCKED.

    * A host cannot block dates that are already confirmed bookings.

    * Deletion: A property cannot be deleted if there are any active or future confirmed bookings.

## 4. Performance Criteria
* Create/Update Property: Should complete within 200ms for 95% of requests (including image URL storage).

* Get Property Details: Should complete within 80ms for 95% of requests.

* Get Host's Properties: Should complete within 150ms for 95% of requests, even with a large number of properties per host.

* Delete Property: Should complete within 100ms for 95% of requests.

* Concurrency: The system should handle at least 200 concurrent property management requests per second.

* Data Consistency: Ensure atomicity for property creation/updates.

* Search Optimization: The system should support efficient searching and filtering of properties for guests, which implies proper indexing on relevant fields (e.g., location, price, number of guests).















