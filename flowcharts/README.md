# Workflow and Processes for Backend Feature: Property Booking
This workflow describes what happens on the backend from the moment a guest initiates a booking request until it's confirmed or rejected.

## Feature Chosen: Property Booking (Guest initiates a booking)

### Actors/Entities Involved:

* Guest (User): Initiates the booking.

* Host (User): Owns the property and approves/rejects the booking.

* Backend System: The core application logic.

* Database (Data Stores):

   * Listings (Property details, availability calendar)

   * Bookings (Pending, confirmed, rejected bookings)

   * Users (Guest and Host profiles)

   * Payments (Transaction records)

* Payment Gateway (External Service): Processes payments.

* Notification Service (External Service): Sends emails/SMS.

### Flowchart Steps Description (for Draw.io):

* Start: Guest clicks "Request to Book" button on a Listing Page.

1- Receive Booking Request (Backend)

Input: Property ID, Check-in Date, Check-out Date, Number of Guests, Guest User ID.

Process: Validate basic input (e.g., dates are in future, guest count is valid).

Next: Proceed to availability check.

2- Check Property Availability (Backend)

Input: Property ID, Check-in Date, Check-out Date.

Process: Query Listings database for the specific property's availability calendar. Check for conflicts with existing confirmed bookings or host-blocked dates for the requested period.

Output: Availability Status (Available/Not Available).

Decision Point: Is the property available for the requested dates?

NO: Go to Step 2a (Notify Guest - Not Available).

YES: Go to Step 3 (Calculate Booking Price).

2a. Notify Guest - Not Available (Backend & Notification Service)

Process: Mark booking request as Rejected - Not Available. Send notification to Guest.

Output: Booking Rejection Confirmation.

Next: End Flow (Booking Failed).

3- Calculate Booking Price (Backend)

Input: Property ID, Check-in Date, Check-out Date, Number of Guests.

Process: Retrieve Base Price, Per-Night Price, Cleaning Fee, Service Fee (Guest), Host Payout Percentage from Listings and System Configurations. Calculate total price, including all fees.

Output: Calculated Total Price, Host Payout Amount.

Next: Proceed to Pre-Authorization.

4- Initiate Payment Pre-Authorization (Backend & Payment Gateway)

Input: Guest User ID, Calculated Total Price, Payment Method Details (Tokenized).

Process: Send request to Payment Gateway to pre-authorize the calculated total price on the guest's payment method. This holds the funds without charging them yet.

Output: Pre-Authorization Status (Success/Failure), Payment Gateway Transaction ID.

Decision Point: Is pre-authorization successful?

NO: Go to Step 4a (Notify Guest - Payment Failed).

YES: Go to Step 5 (Create Pending Booking Record).

4a. Notify Guest - Payment Failed (Backend & Notification Service)

Process: Mark booking request as Rejected - Payment Failed. Send notification to Guest.

Output: Booking Rejection Confirmation.

Next: End Flow (Booking Failed).

5- Create Pending Booking Record (Backend & Database)

Input: Property ID, Guest User ID, Check-in Date, Check-out Date, Calculated Total Price, Pre-Authorization Transaction ID, Booking Status: PENDING.

Process: Store all booking details in the Bookings database table.

Output: Booking ID, Pending Booking Record.

Next: Proceed to Notify Host.

6- Notify Host of New Booking Request (Backend & Notification Service)

Input: Booking ID, Guest Details, Property ID, Booking Dates, Calculated Total Price.

Process: Retrieve Host's contact details from Users database. Send an email/SMS notification to the Host about the new pending booking request, prompting them to approve or reject. Include a link to manage the booking.

Output: Host Notification Sent Confirmation.

Next: End Flow (Waiting for Host Action).

Subsequent Flow (triggered by Host action):

Start: Host responds to the booking request (via web dashboard or email link).

7- Receive Host Action (Approve/Reject) (Backend)

Input: Booking ID, Host User ID, Action (Approve/Reject), (Optional: Reason for Rejection).

Process: Validate host's ownership of the property for the given booking ID.

Decision Point: Has the Host approved or rejected?

Approved: Go to Step 8 (Confirm Booking & Charge Payment).

Rejected: Go to Step 9 (Cancel Pre-Authorization & Notify Guest).

8- Confirm Booking & Charge Payment (Backend & Payment Gateway & Database)

Input: Booking ID, Pre-Authorization Transaction ID.

Process:

Send request to Payment Gateway to capture the pre-authorized funds.

Update Bookings database: Set Booking Status to CONFIRMED.

Record final Transaction Details in Payments database.

Update Listings calendar to block the dates.

Output: Payment Capture Status, Confirmed Booking Record.

Next: Go to Step 8a (Notify Guest & Host - Confirmed).

8a. Notify Guest & Host - Confirmed (Backend & Notification Service)

Process: Send Booking Confirmation email/SMS to Guest. Send Booking Approved notification to Host (including payout details).

Output: Confirmation Notifications Sent.

Next: End Flow (Booking Confirmed).

9- Cancel Pre-Authorization & Notify Guest (Backend & Payment Gateway & Database & Notification Service)

Input: Booking ID, Pre-Authorization Transaction ID, (Optional: Reason for Rejection).

Process:

Send request to Payment Gateway to void/cancel the pre-authorization.

Update Bookings database: Set Booking Status to REJECTED.

Send Booking Rejected email/SMS to Guest (mentioning pre-authorization released).

Send Booking Rejected notification to Host.

Output: Pre-Authorization Void Status, Rejected Booking Record.

Next: End Flow (Booking Rejected).

Draw.io Elements to Use:

Rounded Rectangles/Ovals: Start/End points.

Rectangles: Process steps (e.g., "Check Property Availability").

Diamonds: Decision points (e.g., "Is property available?").

Cylinders/Database Icons: Data Stores (e.g., "Listings Database").

Rectangles with Wavy/Jagged Edges (or specific external entity symbols): External Entities (e.g., "Payment Gateway", "Notification Service").

Arrows: Data flow and process flow direction. Label data flows with the data being passed.

By following this detailed description, you should be able to create a comprehensive and accurate flowchart in Draw.io for the "Property Booking" backend process.












Deep Research

Canvas


