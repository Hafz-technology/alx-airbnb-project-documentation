# Airbnb Clone Backend Use Case Diagram Outline
## This outline provides the necessary components to construct a Use Case Diagram for your Airbnb Clone backend. It details the actors, the functionalities (use cases) they interact with, and the relationships between these elements.

## 1. Actors
### Actors represent the entities that interact with the system.

* Guest: A user who searches for properties, books stays, makes payments, and leaves reviews.

* Host: A user who lists properties, manages bookings, and responds to reviews.

* Admin: A privileged user who manages the entire system (users, listings, bookings, payments).

* Payment Gateway: An external system responsible for processing financial transactions (e.g., Stripe, PayPal).

* Email Service: An external system responsible for sending email notifications (e.g., SendGrid, Mailgun).

## 2. Use Cases (System Functionalities)
### Use cases represent a specific functionality or service provided by the system. They are grouped by core functionality areas.

### 2.1. User Management
* Register Account: Allows new users to create an account.

* (Include) Secure Password Hashing

* Login Account: Allows registered users to sign in.

* (Extend) Login via OAuth (e.g., Google, Facebook)

* Manage Profile: Allows users to update their personal information.

* (Include) Update Contact Information

* (Include) Update Profile Photo

* Manage User Roles: Admin assigns or modifies user roles.

### 2.2. Property Listings Management
*Add Property Listing: Host creates a new property listing.

* (Include) Provide Property Details

* (Include) Upload Property Images

* Edit Property Listing: Host modifies an existing property listing.

* Delete Property Listing: Host removes a property listing.

* Search Properties: Guest searches for properties based on various criteria.

* (Include) Filter by Location

* (Include) Filter by Price Range

* (Include) Filter by Number of Guests

* (Include) Filter by Amenities

* (Include) Apply Pagination

* View Property Details: Guest views detailed information about a specific property.

### 2.3. Booking Management
* Book Property: Guest reserves a property for specific dates.

* (Include) Check Property Availability

* (Include) Calculate Total Price

* (Include) Initiate Payment

* Cancel Booking: Guest or Host cancels a confirmed booking.

* View My Bookings: Guest views their own bookings; Host views bookings for their properties.

* Update Booking Status: Host changes the status of a booking (e.g., pending to confirmed).

### 2.4. Payment Integration
* Process Payment: System interacts with a payment gateway to finalize a booking payment.

* (Include) Interact with Payment Gateway

* Handle Payouts: System manages payouts to hosts. (Often an internal system process triggered by booking completion, but can be represented as an interaction with the Payment Gateway from the system's perspective).

### 2.5. Reviews and Ratings
* Leave Review: Guest submits a review and rating for a property they have stayed in.

* (Include) Validate Completed Booking

* View Property Reviews: Users (Guest, Host) can see reviews for a property.

* Respond to Review: Host can reply to reviews left on their property.

### 2.6. Notifications System
* Receive Notification: Users (Guest, Host, Admin) receive various system notifications.

* (Include) Send Email Notification (via Email Service)

* (Include) Send In-App Notification

### 2.7. Admin Dashboard
* Manage Users: Admin can view, edit, or delete user accounts.

* Manage Listings: Admin can view, edit, or delete any property listing.

* Manage Bookings: Admin can view or modify any booking.

* Manage Payments: Admin can monitor payment transactions.

## 3. Relationships
### Relationships show how actors interact with use cases and how use cases relate to each other.

* Association: A solid line connecting an actor to a use case, indicating that the actor initiates or participates in the use case.

* Include: A dashed arrow from a base use case to an included use case, labeled <<include>>. This means the base use case always incorporates the functionality of the included use case.

* Extend: A dashed arrow from an extending use case to a base use case, labeled <<extend>>. This means the extending use case optionally adds functionality to the base use case under certain conditions.

* Generalization: A solid line with a hollow triangular arrow from a specialized actor/use case to a more general actor/use case.

### Example Relationships for Diagram:
* Guest --associates with--> Register Account

* Host --associates with--> Register Account

* Guest --associates with--> Login Account

* Host --associates with--> Login Account

* Admin --associates with--> Login Account

* Register Account --includes--> Secure Password Hashing

* Login Account --extends--> Login via OAuth

* Host --associates with--> Add Property Listing

* Add Property Listing --includes--> Upload Property Images

* Guest --associates with--> Search Properties

* Search Properties --includes--> Filter by Location

* Book Property --includes--> Initiate Payment

* Process Payment --associates with--> Payment Gateway

* Receive Notification --includes--> Send Email Notification

* Send Email Notification --associates with--> Email Service

* Admin --associates with--> Manage Users

* Admin --associates with--> Manage Listings

