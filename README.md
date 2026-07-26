# CST8917-Lab7

# FleetBook - Serverless Vehicle Booking System

FleetBook is an event-driven, serverless vehicle booking system built using Azure Service Bus, Azure Functions, and Azure Logic Apps, with a lightweight HTML web client for submitting vehicle booking requests.

---

## 🏗️ Architecture Overview

The system consists of the following components:

1. **Web Client (`client.html`)**
   - Provides a simple user interface for customers to submit booking requests.
   - Sends booking data to the Azure Service Bus queue.

2. **Azure Service Bus Queue (`booking-queue`)**
   - Stores incoming booking requests until they are processed.

3. **Azure Logic App (`process-booking`)**
   - Triggered whenever a new message arrives in the queue.
   - Parses the booking request.
   - Calls the Azure Function.
   - Sends confirmation or rejection emails.
   - Publishes the booking result to a Service Bus topic.

4. **Azure Function (`fleetbook-func`)**
   - Validates vehicle availability.
   - Calculates booking pricing.
   - Returns a booking status of either:
     - `confirmed`
     - `rejected`

5. **Azure Service Bus Topic (`booking-results`)**
   - Receives processed booking results.
   - Routes messages to subscriptions based on their status:
     - `confirmed-sub`
     - `rejected-sub`

---

## 🚀 Setup & Deployment

### 1. Deploy the Azure Function App

Deploy the Azure Function App to Azure.

Verify the deployment by opening the health endpoint:

```text
https://<your-function-app-name>.azurewebsites.net/api/health
```

A successful deployment should return a healthy response.

---

### 2. Configure Azure Service Bus

Create an Azure Service Bus Namespace.

Within the namespace, create:

- Queue: `booking-queue`
- Topic: `booking-results`

Create two subscriptions under the topic:

- `confirmed-sub`
- `rejected-sub`

Under **Shared Access Policies**, locate:

```
RootManageSharedAccessKey
```

Copy the following values:

- SAS Policy Name
- Primary Key

These credentials will be required by the web client.

---

### 3. Create the Azure Logic App

Build (or import) a Logic App with the following workflow:

1. **Trigger**
   - When a message is received in the `booking-queue`.

2. **Parse JSON**
   - Parse the incoming booking request.

3. **HTTP / Azure Function**
   - Invoke the Azure Function to process the booking.

4. **Parse JSON**
   - Parse the Function response.

5. **Condition**
   - Check whether:

   ```
   status == "confirmed"
   ```

6. **If True**
   - Send a booking confirmation email.
   - Publish a message to the `booking-results` topic with label:

   ```
   confirmed
   ```

7. **If False**
   - Send a booking rejection email.
   - Publish a message to the `booking-results` topic with label:

   ```
   rejected
   ```

---

## 🧪 Testing the Application

1. Open `client.html` in a web browser.

2. Expand the **Service Bus Configuration** section.

3. Enter:

   - **Service Bus Namespace**
   - **SAS Policy Name**
     ```
     RootManageSharedAccessKey
     ```
   - **SAS Key**
     - Paste the Primary Key copied from Azure Service Bus.

4. Submit test bookings.

### Confirmed Booking

Submit a booking with:

- **Location:** Ottawa
- **Vehicle:** Sedan

Expected result:

- ✅ Green confirmation badge
- ✅ Confirmation email sent
- ✅ Message published to `confirmed-sub`

---

### Rejected Booking

Submit a booking with:

- **Location:** Montreal
- **Vehicle:** Sedan

Expected result:

- ❌ Red rejection badge
- ❌ Rejection email sent
- ❌ Message published to `rejected-sub`

---
