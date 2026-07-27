# CST8917 Lab 3: FleetBook — Vehicle Booking with Service Bus, Logic Apps & Functions

---
**Student Name**: Naveed Hossain             **Student ID**: 0410818822 
**Course**: CST8917 - Serverless Applications
**Semester**: Summer 2026
---

Video Demo: https://youtu.be/9MuNTkj_jRs 

---

## Architecture

1. **Web Client (`client.html`)** – Sends booking requests to the Service Bus queue.
2. **Service Bus Queue (`booking-queue`)** – Stores incoming bookings.
3. **Logic App (`process-booking`)** – Processes requests, calls the Azure Function, sends emails, and publishes results.
4. **Azure Function (`fleetbook-func`)** – Checks availability, calculates pricing, and returns `confirmed` or `rejected`.
5. **Service Bus Topic (`booking-results`)** – Routes results to:
   - `confirmed-sub`
   - `rejected-sub`

---

## Setup

### Azure Function
Deploy the Function App and verify it using:

```text
https://<your-function-app-name>.azurewebsites.net/api/health
```

### Azure Service Bus
Create:

- Queue: `booking-queue`
- Topic: `booking-results`
- Subscriptions:
  - `confirmed-sub`
  - `rejected-sub`

Copy the **Primary Key** from `RootManageSharedAccessKey`.

### Azure Logic App
Create a workflow that:

1. Triggers on messages from `booking-queue`.
2. Parses the booking request.
3. Calls the Azure Function.
4. Parses the response.
5. Sends a confirmation or rejection email.
6. Publishes the result to `booking-results`.

---

## Testing

1. Open `client.html`.
2. Enter:
   - Service Bus Namespace
   - SAS Policy: `RootManageSharedAccessKey`
   - Primary Key
3. Submit test bookings:
   - **Ottawa (Sedan):** `confirmed`
   - **Montreal (Sedan):** `rejected`
