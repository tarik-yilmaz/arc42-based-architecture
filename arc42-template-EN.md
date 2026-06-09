# youRide Architecture Documentation
<br><br>

# Introduction and Goals
<br>

This document describes the architecture of youRide, an Austrian ride
sharing application created by a small startup team. The system provides
an affordable and convenient transportation option while focusing on a
lean MVP, fast market entry, paying customers, and cost-efficient
operation.
<br><br>

## Requirements Overview


### Business Goals


youRide connects customers who need transportation with private or
professional drivers who can offer rides. The application starts in
Austria and positions itself as a local and cheaper alternative to large
international ride sharing providers.
<br>

The main business goal is to gain paying customers as quickly as
possible and become profitable with low operating costs. Future investor
interest is expected to follow from growth in active customers, active
drivers, completed rides, and controlled infrastructure costs. The
business model for the MVP is based on commissions per completed ride;
additional revenue streams are not part of the initial scope.
<br><br>

| ID | Business Goal | Description |
|----|---------------|-------------|
| BG_1 | Fast market entry | Build a useful MVP quickly in order to validate the Austrian market. |
| BG_2 | Paying customers | Win active customers and drivers and generate revenue through commissions per completed ride. |
| BG_3 | Low operating costs | Keep infrastructure and tool costs low because the founders provide and acquire the initial capital themselves. |
| BG_4 | Local alternative | Offer an Austrian ride sharing alternative that is cheaper than large international providers. |
| BG_5 | Measurable growth | Track active customers, active drivers, completed rides, and operating costs as main success indicators. |
<br><br>

### Functional Requirements Overview


The MVP supports spontaneous ride booking as well as planned shared
rides for a later time. Customers and drivers use separate account types,
because both groups have different workflows and data requirements.
<br><br>

| ID | Essential Feature | Description |
|----|-------------------|-------------|
| REQ_1 | Register account | Customers and drivers can create separate accounts and access the application. |
| REQ_2 | Verify driver | The company can verify drivers before they offer rides on the platform. |
| REQ_3 | Search for rides | Customers can search for available spontaneous or planned rides that match their travel needs. |
| REQ_4 | Book a ride | Customers can request or book a ride and receive a calculated ride price before the ride starts. |
| REQ_5 | Match driver automatically | The system automatically matches a suitable driver to a customer request. |
| REQ_6 | Accept or decline ride request | Drivers can accept or decline incoming ride requests. |
| REQ_7 | Track ride | Customers and drivers can track the ride with live GPS on a map and the status values requested, accepted, in progress, completed, or cancelled. |
| REQ_8 | Cancel ride | Customers and drivers can cancel a ride before completion. |
| REQ_9 | Complete ride and process payment | Drivers and customers can complete a ride once the trip has finished, and payment is processed through the external payment provider. |
| REQ_10 | View ride history | Customers and drivers can view previously completed or cancelled rides. |
| REQ_11 | Administer MVP | The founding team can review users, verify drivers, inspect rides, and access basic operational data. |
<br>

### Requirements Sources


The requirements overview is based on the existing project documents in
the `ridesharing` folder. The table above summarizes the most important
requirements for architecture work; detailed use-case descriptions remain
in the referenced documents.
<br><br>

| Document | Purpose |
|----------|---------|
| `ridesharing/brd.md` | Business goals, scope, functional requirements, non-functional requirements, assumptions, and constraints. |
| `ridesharing/use-case-document.md` | Actors, use cases, preconditions, postconditions, and user interactions for the MVP. |
| `ridesharing/investor-interview.md` | Investor expectations, market opportunity, return expectations, and business risks. |
<br><br>

## Quality Goals


The following quality goals are ordered by priority and drive the main
architectural decisions.
<br><br>

| ID | Priority | Quality Goal | Motivation | Concrete Scenario |
|----|----------|--------------|------------|-------------------|
| QG_1 | 1 | Functional suitability | Price calculations, driver matching, ride data, location data, ride status changes, and communication between drivers and customers must work correctly and reliably. | When a customer books a ride, the system calculates a price, assigns a suitable driver, stores the ride, and shows the same ride status to customer and driver. |
| QG_2 | 2 | Usability | The application must be intuitive and usable without documentation for customers and drivers, because an easy-to-use platform helps win early users and supports fast startup growth. | A new customer can register, search for a ride, see the price, and request the ride without reading external instructions. |
| QG_3 | 3 | Scalability | The system must support growth in active customers, active drivers, completed rides, and later cloud usage, without creating high initial infrastructure costs. | When demand grows beyond the rented Linux server, the architecture allows migration of selected infrastructure parts to cloud services while keeping the server available for backup. |
<br><br>

## Stakeholders


The following stakeholders influence the architecture and the required
level of detail in this documentation.
<br><br>

| Role/Name | Contact | Expectations |
|-----------|---------|--------------|
| Entrepreneurs / developers | Founding team | A cost-efficient MVP, fast implementation, simple deployment, low operating costs, and an architecture that can later be scaled and split into microservices if growth requires it. |
| Customers | - | Affordable and convenient transportation, simple ride booking for immediate or planned rides, transparent prices, live ride status, and a good user interface experience. |
| Drivers | - | Intuitive application, clear ride requests, reliable customer communication, live ride status, and a good price-to-earnings ratio. |
| DevOps / network employee | Internal employee | Reliable deployment, stable server and network operation, monitoring, backups, and a technical setup that can later integrate cloud services. |
| Controlling employee | Internal employee | Cost overview, revenue data, ride-based commission reporting, and basic operational reporting for business decisions. |
<br><br>

# Architecture Constraints


The following constraints limit the architectural freedom for the first
release of youRide. They are separated from architecture decisions, so
that later decisions can be traced back to the actual restrictions.
<br><br>

| ID | Category | Constraint | Background and Motivation | Architectural Consequence |
|----|----------|------------|---------------------------|---------------------------|
| C_1 | Budget / operational | The initial production system runs on a rented Linux server. | This keeps fixed infrastructure costs low for the startup during the MVP phase. | The initial deployment must be simple enough to run on one server and must not depend on a complex cloud setup. |
| C_2 | Budget | Recurring infrastructure and tool costs must be kept as low as reasonably possible. | The founders provide and acquire the initial capital themselves. | The architecture must favor a lean MVP, avoid unnecessary paid services, and keep operating costs visible. |
| C_3 | Operational / scalability | Cloud services are used later when scaling becomes necessary; the rented server remains available for backup purposes. | The startup wants to start cheaply but still keep a growth path for increasing customer, driver, and ride numbers. | The system must avoid unnecessary vendor lock-in and keep deployment, data export, and backup concepts compatible with a later cloud migration. |
| C_4 | Organizational | The initial company team consists of three entrepreneurs/developers, one DevOps/network employee, and one controlling employee. | Development, operations, networking, and financial control are handled by a small internal team. | The architecture, deployment process, monitoring, and documentation must be understandable and maintainable by a small team. |
| C_5 | Compliance | Data governance is based on GDPR. | Customer profiles, driver profiles, ride data, and location data are personal data. | Privacy, security, retention, access control, and deletion concepts must respect GDPR principles from the beginning. |
<br>

### Non-constraints

The chosen technologies, such as Java, Angular, MySQL, and REST APIs,
are current architecture decisions and not externally mandated
constraints. They will be justified in the solution strategy and
architecture decisions sections.
<br><br>

# System Scope and Context


For this documentation, the youRide system boundary includes the mobile
app/frontend, the backend monolith, the MySQL database, and the admin
functions used by the founding team. 

External systems used in the MVP
are an external authentication provider, the payment provider Stripe,
and an external maps/geocoding/routing provider. Map display, ride
matching, price calculation, live tracking, reporting, and
administration are treated as youRide functionality; the maps provider
only supplies technical map, routing, geocoding, distance, and ETA data.
<br><br>

## Business Context
<br>

![business-context-diagramm](./resource/business-context.png)
<br><br>

The following table shows youRide as a black box and lists the external
communication partners and the domain-specific information exchanged
with them.
<br><br>

| Communication Partner | Input to youRide | Output from youRide |
|-----------------------|------------------|---------------------|
| Customers | Registration data, login requests, pickup and destination, ride search criteria, booking requests, cancellation requests, live location data, ride completion confirmation. | Available rides or matched driver, calculated price, booking confirmation, ride status, live ride location, ride history. |
| Drivers | Registration data, login requests, driver verification data, ride availability, accept or decline decisions, live location data, cancellation requests, ride completion confirmation. | Ride requests, customer pickup and destination, calculated ride information, ride status, customer communication, ride history. |
| Founding team / administrators | Driver verification decisions, user review actions, ride inspection requests, operational corrections. | User overview, driver verification status, ride overview, basic operational data. |
| Controlling employee | Reporting requests and cost or revenue analysis requests. | Ride-based commission data, revenue data, cost overview, and basic operational reports. |
| External authentication provider | Registration and login requests, identity verification requests. | Authentication result, user identity information, and authentication tokens. |
| Stripe payment provider | Payment request, ride price, payer and payee references, payment confirmation request. | Payment status, transaction reference, and payment confirmation or failure information. |
| External maps/geocoding/routing provider | Route, distance, ETA, geocoding, reverse geocoding, and map display data. | Pickup and destination addresses or coordinates and the minimum location data needed for map, route, ETA, and geocoding requests. |
<br><br>

There are no additional external business partners such as authorities,
insurance providers, or transportation agencies in the MVP scope.
<br><br>

## Technical Context
<br>

![technical-context-diagramm](./resource/technical-context.png)
<br><br>

The MVP uses simple and well-known technical interfaces to keep
implementation and operation manageable for the small team. Normal app
operations use HTTPS/REST. Live ride tracking uses WebSocket
communication because both driver and customer need timely location and
status updates during an active ride. 

Payment processing is decoupled
through an internal durable message queue so that temporary network
errors, Stripe outages, or backend retries do not lose payment requests.
Map, route, ETA, and geocoding information is retrieved through an
external provider API, while ride matching and price calculation rules
remain inside youRide.
<br><br>

| Technical Interface | Communication Partner / Target | Channel / Protocol | Purpose |
|---------------------|--------------------------------|--------------------|---------|
| Mobile app to backend | youRide backend monolith | HTTPS/REST over public internet | Registration, login handoff, ride search, booking, cancellation, ride completion, ride history, and admin-related requests. |
| Live tracking channel | youRide backend monolith | WebSocket over TLS | Driver and customer live location updates and ride status changes during an active ride. |
| Backend to database | MySQL database | Internal database connection on the rented Linux server or private network | Store and read customer profiles, driver profiles, rides, ride status, prices, payment references, and reporting data. |
| Backend to authentication provider | External authentication provider | HTTPS, based on standard authentication protocols such as OAuth 2.0 / OpenID Connect | Register users, authenticate users, and validate identity information. |
| Backend/mobile app to maps provider | External maps/geocoding/routing provider | HTTPS / Maps, Routing, and Geocoding APIs | Resolve pickup and destination locations, retrieve route distance and ETA, support reverse geocoding, and display map information. |
| Backend to payment queue | Internal payment message queue | Durable internal queue, for example AMQP / RabbitMQ or a comparable managed queue | Store payment requests reliably before provider communication, so payments can be retried after temporary network, provider, or backend errors. |
| Payment worker to Stripe | Stripe payment provider | HTTPS / Stripe API with retries and idempotency keys | Process queued payment requests, retrieve payment status, and store payment references for ride commissions. |
| DevOps access | Rented Linux server | Secure administrative access, e.g. SSH via restricted network access | Deployment, monitoring, backup handling, and operational maintenance. |
<br>

The detailed physical deployment of the rented server, database,
networking, backup, and later cloud migration path is described in the
deployment view.
<br><br>

**Mapping Input/Output to Channels**
<br><br>

| Domain Input/Output | Technical Channel |
|---------------------|-------------------|
| Registration, ride search, booking, cancellation, completion, ride history | Mobile app to backend via HTTPS/REST. |
| Live GPS location and active ride status | WebSocket over TLS between mobile app and backend. |
| Pickup/destination geocoding, route distance, ETA, and map display data | Backend or mobile app to external maps/geocoding/routing provider via HTTPS / Maps API, with only required location data sent. |
| Authentication result and user identity | Backend to external authentication provider via HTTPS. |
| Payment request and retry state | Backend to internal payment message queue through a durable queue channel. |
| Payment confirmation and transaction reference | Payment worker to Stripe via HTTPS / Stripe API, with the result persisted in youRide. |
| Stored customer, driver, ride, price, payment, and reporting data | Backend to MySQL via internal database connection. |
<br><br>

# Solution Strategy

The following solution strategies form the cornerstones of the
architecture and provide the basis for more detailed implementation
decisions.
<br><br>

| Goal / Requirement | Architectural Approach | Rationale / Linked Constraints and Goals |
|--------------------|------------------------|------------------------------------------|
| Fast market entry and low operating costs (`BG_1`, `BG_3`, `C_1`, `C_2`) | Build the MVP as a modular monolith. The system is deployed as one backend application, but internally structured into clear modules such as identity, ride management, payment integration, tracking, and administration. | A modular monolith keeps deployment and operations simple for the small team while still supporting maintainability through internal boundaries. |
| Familiar and productive technology stack | Use Java for the backend monolith, Angular for the frontend, MySQL for persistence, REST APIs for normal app communication, and WebSocket communication for live tracking. | These technologies are established, affordable, and suitable for the planned MVP scope. They also support the chosen client-server and monolithic architecture. |
| External authentication and payment (`REQ_1`, `REQ_4`, `REQ_9`, `C_2`) | Use an external authentication provider and Stripe as external payment provider. Payment requests are handed off through an internal durable queue before Stripe communication. | Authentication and payment are complex and security-critical. Delegating them reduces implementation effort and risk for the startup, while queued payment handoff reduces the effect of temporary network or provider errors. |
| Cost-efficient deployment and later scalability (`QG_3`, `C_1`, `C_3`) | Start on a rented Linux server. When demand grows, add cloud services for scaling while keeping the rented server available for backup purposes. | This keeps initial costs low but leaves a realistic growth path for increasing customer, driver, and ride numbers. |
| Functional suitability and reliability (`QG_1`) | Use unit tests and integration tests for important business logic and external interfaces. | Price calculation, ride status changes, matching, persistence, authentication handoff, and payment integration must work reliably. |
| Security and data protection (`C_5`) | Use penetration tests and GDPR-oriented data governance. | Customer, driver, ride, location, and payment reference data require careful protection from the beginning. |
<br><br>

# Building Block View
<br>

## Whitebox Overall System

### Overview Diagram

The Level 1 overview diagram is maintained in `building-blocks.puml`.
It shows youRide as a complete system with mobile app/frontend, backend
monolith, database, and the two external providers for authentication
and payment.
<br><br>

![level1](./resource/level1.png)
<br><br>

### Motivation

The decomposition follows the selected modular monolith strategy. The
mobile app/frontend is separated from the backend so that user
interaction stays independent from business logic. 

The backend monolith
contains the core business capabilities and is deployed as one
application to keep operation simple for the startup. MySQL is separated
as the persistent data store. Payment requests are first written to an
internal durable message queue and then processed by a payment worker.

Authentication and payment execution are delegated to external providers
because they are security-critical and costly to implement from scratch.
<br><br>

### Contained Building Blocks and Important External Systems
<br>

| Name | Type | Responsibility | Interfaces | Fulfilled Requirements |
|------|------|----------------|------------|------------------------|
| youRide Mobile App / Frontend | Contained building block | Provides the user interface for customers, drivers, administrators, and controlling. It supports registration, driver verification, ride search, booking, live tracking, cancellations, ride completion, ride history, administration, and reporting access. | HTTPS/REST and WebSocket/TLS to the backend monolith. | `REQ_1`, `REQ_2`, `REQ_3`, `REQ_4`, `REQ_6`, `REQ_7`, `REQ_8`, `REQ_9`, `REQ_10`, `REQ_11` |
| youRide Backend Monolith | Contained building block | Implements the central application logic for identity integration, customer and driver management, driver verification, ride matching, ride status handling, live tracking, payment integration, administration, and reporting. | HTTPS/REST and WebSocket/TLS for the frontend, HTTPS to the authentication provider, durable payment queue access, and internal database connection to MySQL. | `REQ_1` to `REQ_11` |
| MySQL Database | Contained building block | Stores customer profiles, driver profiles, verification status, ride data, ride status, calculated prices, payment references, and reporting data. | Internal database connection from the backend monolith. | `REQ_1`, `REQ_2`, `REQ_3`, `REQ_4`, `REQ_5`, `REQ_6`, `REQ_7`, `REQ_8`, `REQ_9`, `REQ_10`, `REQ_11` |
| Internal Payment Message Queue | Contained building block | Durably stores payment requests, retry metadata, and idempotency information until the payment worker can process them. | Internal durable queue channel used by Payment Integration and the Payment Worker. | `REQ_9`, `BG_2`, `QG_1` |
| External Auth Provider | External system | Handles registration, login, authentication, and token issuing for customer, driver, and internal users. | HTTPS, based on standard authentication protocols such as OAuth 2.0 / OpenID Connect. | `REQ_1` |
| Stripe Payment Provider | External system | Processes queued ride payments and returns payment status and transaction references. | HTTPS / Stripe API used by the payment worker. | `REQ_9`, `BG_2` |
<br><br>

### Important Interfaces
<br>

| Interface | Description |
|-----------|-------------|
| Frontend REST API | Main interface for registration handoff, ride search, booking, cancellation, completion, ride history, administration, and reporting requests. |
| Live Tracking Channel | WebSocket/TLS channel for active ride status and live GPS updates between frontend and backend. |
| Authentication Provider API | External interface used by the backend to validate user identity and authentication tokens. |
| Payment Queue | Internal durable queue used to store payment requests before provider communication and to support retries after temporary failures. |
| Stripe API | External payment interface used by the payment worker to process queued ride payments and retrieve payment references. |
| Database Access | Internal persistence interface between backend monolith and MySQL. |
<br><br>

## Level 2

### White Box youRide Backend Monolith
<br>

The backend monolith is refined on Level 2 because it contains the most
important business logic and the main architectural risks. 

The mobile app/frontend, MySQL database, and external providers remain black boxes
on this level because their internal structure is either less critical
for the MVP architecture or outside the youRide implementation scope.
<br><br>

![level2](./resource/level2.png)
<br><br>

| Name | Responsibility | Main Collaborators |
|------|----------------|--------------------|
| Identity / Auth Integration | Integrates with the external authentication provider, validates authentication tokens, and maps authenticated users to customer, driver, or internal roles. | External Auth Provider, Customer Management, Driver Management & Verification, Administration & Reporting |
| Customer Management | Manages customer profile data and customer-specific ride access. | Identity / Auth Integration, Ride Management & Matching, Persistence |
| Driver Management & Verification | Manages driver profile data, driver availability, and driver verification status before drivers can offer rides. | Identity / Auth Integration, Ride Management & Matching, Administration & Reporting, Persistence |
| Ride Management & Matching | Handles ride requests, automatic driver matching, calculated prices, ride status transitions, cancellation, completion, and ride history. | Customer Management, Driver Management & Verification, Live Tracking, Payment Integration, Persistence |
| Live Tracking | Processes live GPS updates and distributes active ride status and location updates to customer and driver. | Ride Management & Matching, Mobile App / Frontend |
| Payment Integration | Creates durable payment requests, writes them to the internal payment queue, coordinates the payment worker, and stores payment references and payment status. | Ride Management & Matching, Internal Payment Message Queue, Stripe Payment Provider, Persistence |
| Administration & Reporting | Supports founder/admin operations, driver verification decisions, ride inspection, basic revenue data, commission reporting, and cost overview. | Driver Management & Verification, Ride Management & Matching, Payment Integration, Persistence |
| Persistence | Provides database access for the backend modules and isolates MySQL access from business logic. | MySQL Database, all backend modules |
<br><br>

### Level 2 Interfaces

| Interface | Description |
|-----------|-------------|
| Application API | REST endpoints exposed by the backend monolith for frontend use cases. |
| Tracking API | WebSocket/TLS endpoint for live ride location and ride status updates. |
| Module-internal services | Internal Java service interfaces between backend modules. They keep module boundaries explicit inside the monolith. |
| Repository interfaces | Internal persistence interfaces used by backend modules to access stored data through the Persistence module. |
<br><br>

## Level 3

### White Box Ride Management & Matching

Ride Management & Matching is refined on Level 3 because it is the core
business module of youRide. It supports the most important MVP
requirements: ride search, booking, automatic driver matching, ride
status handling, cancellation, completion, ride history, and the payment
handoff.
<br><br>

![level3](./resource/level3.png)
<br><br>

### Motivation


This module must keep the ride lifecycle consistent. Customers and
drivers must see the same ride state, price, and matching result. 

The module also coordinates with Driver Management & Verification, Live
Tracking, Payment Integration, and Persistence. Therefore, its internal
structure is more relevant than the internal structure of simpler or
external building blocks.
<br><br>

### Internal Building Blocks

| Name | Responsibility | Main Collaborators |
|------|----------------|--------------------|
| Ride Application Service | Orchestrates ride use cases such as search, request, accept, cancel, start, complete, and history access. | Application API, Ride Aggregate, Matching Service, Price Calculation, Ride Repository Port, Payment Integration |
| Ride Aggregate | Represents one ride and protects ride invariants such as customer, driver, pickup, destination, price, payment reference, and current status. | Ride Application Service, Ride Status Policy |
| Matching Service | Selects a suitable verified and available driver for a ride request. | Driver Management & Verification, Live Tracking, Ride Application Service |
| Price Calculation | Calculates the ride price before booking and provides the price used for the later payment request. | Ride Application Service, Persistence |
| Ride Status Policy | Validates allowed status transitions, for example from `requested` to `accepted` or from `in progress` to `completed`. | Ride Aggregate, Ride Application Service |
| Ride Repository Port | Defines persistence operations for ride requests, status changes, prices, payment references, and ride history. | Persistence, MySQL Database |
| Payment Handoff | Coordinates ride completion with Payment Integration so that the payment request is durably queued before the ride is finally stored as completed. | Payment Integration, Ride Application Service |
<br><br>

### Important Interfaces


| Interface / Operation | Description |
|-----------------------|-------------|
| `searchRides(...)` | Returns ride options or availability information for spontaneous or planned rides. |
| `requestRide(customerId, pickup, destination, time)` | Creates a ride request, calculates a price, matches a driver, and stores the ride with status `requested`. |
| `acceptRide(rideId, driverId)` | Validates the assigned driver and changes the ride status to `accepted`. |
| `startRide(rideId)` | Changes the ride status to `in progress` when the ride begins. |
| `cancelRide(rideId, actorId)` | Cancels a ride when the current status allows cancellation. |
| `completeRide(rideId)` | Completes a ride after the payment request has been durably queued through Payment Integration. |
| `getRideHistory(userId)` | Returns completed or cancelled rides for customer or driver history views. |
<br><br>

### Ride Status Transitions

| Current Status | Trigger | Next Status | Notes |
|----------------|---------|-------------|-------|
| `requested` | Matching result is available and driver accepts the request. | `accepted` | Driver must be verified and allowed to accept the ride. |
| `requested` | Customer or system cancels before acceptance. | `cancelled` | No payment is processed. |
| `accepted` | Driver and customer start the ride. | `in progress` | Live tracking continues through the Live Tracking module. |
| `accepted` | Customer or driver cancels before the ride starts. | `cancelled` | Cancellation is stored for ride history and reporting. |
| `in progress` | Ride reaches the destination and the payment request is durably queued. | `completed` | Payment status can remain `pending` or `retrying` until the payment worker stores the Stripe payment reference. |
| `in progress` | Exceptional cancellation during the ride. | `cancelled` | Requires business rules for later refinement. |
<br><br>

### Quality and Risk Notes

-   Supports `REQ_3` to `REQ_10`, `QG_1`, `QG_2`, and `QG_3`.
-   Direct database access is avoided through the Ride Repository Port.
-   Payment details are delegated to Payment Integration, the internal
    payment queue, the payment worker, and Stripe.
-   The module must be kept cohesive because it is a major risk area for
    monolith complexity and scaling limitations.
<br><br>

# Runtime View

The following runtime scenarios describe the most relevant MVP workflows
on architecture level. They focus on interactions between the building
blocks from the building block view and cover the most important
successful flows as well as payment failure and retry behavior at the
critical Stripe interface.
<br><br>

## Runtime Scenario 1: Customer Books a Ride with Automatic Driver Matching

![Runtime Scenario 1](./resource/scenario1.png)

1. The customer enters pickup, destination, and desired time in the
   youRide Mobile App / Frontend.
2. The frontend sends the ride request to the backend monolith via the
   Frontend REST API.
3. Identity / Auth Integration validates the customer's authentication
   token using the external authentication provider integration, for
   example with provider metadata and cached public keys.
4. Ride Management & Matching validates the request data and asks Driver
   Management & Verification for verified and available drivers.
5. Ride Management & Matching calculates the ride price and selects a
   suitable driver automatically.
6. Persistence stores the ride request, calculated price, selected
   driver, and initial ride status.
7. The backend returns the booking result to the frontend.
8. The customer sees the calculated price, matched driver information,
   and current ride status.
<br><br>

**Notable aspects:**

-   The scenario supports `REQ_3`, `REQ_4`, and `REQ_5`.
-   Authentication is external, but ride matching and price calculation
    remain core youRide functionality.
-   The ride is stored before later status changes are processed, so the
    ride history and reporting data can be derived from persistent state.
<br><br>

## Runtime Scenario 2: Driver Accepts Ride and Live Tracking Starts

![Runtime Scenario 2](./resource/scenario2.png)

1. The driver receives the ride request in the youRide Mobile App /
   Frontend.
2. The driver accepts the ride request.
3. The frontend sends the accept decision to the backend monolith via the
   Frontend REST API.
4. Identity / Auth Integration validates the driver's authentication
   token.
5. Ride Management & Matching checks that the ride is still open and
   that the driver is allowed to accept it.
6. Persistence stores the ride status as `accepted`.
7. The frontend opens the live tracking channel to the backend via
   WebSocket/TLS.
8. Live Tracking receives driver GPS updates and distributes active ride
   status and location information to the customer and driver frontend.
9. When the ride starts, Ride Management & Matching changes the ride
   status to `in progress` and Persistence stores the status change.
<br><br>

**Notable aspects:**

-   The scenario supports `REQ_6` and `REQ_7`.
-   REST is used for the accept command, while WebSocket/TLS is used for
    continuous live tracking updates.
-   Ride status changes are persisted so that both customer and driver
    see the same current state.
<br><br>

## Runtime Scenario 3: Ride is Completed and Payment is Processed

![Runtime Scenario 3](./resource/scenario3.png)

1. The driver or customer confirms that the ride has reached the
   destination.
2. The frontend sends the completion request to the backend monolith via
   the Frontend REST API.
3. Identity / Auth Integration validates the authenticated user.
4. Ride Management & Matching checks that the ride is currently `in
   progress` and can be completed.
5. Payment Integration creates a payment request with the ride price,
   payment references, retry metadata, and an idempotency key.
6. Payment Integration writes the payment request to the internal durable
   payment queue.
7. Ride Management & Matching changes the ride status to `completed`
   after the queue confirms durable storage of the payment request.
8. Persistence stores the completed ride, final ride status, payment
   status `pending`, and data needed for ride history and controlling
   reports.
9. The payment worker reads the queued payment request and calls Stripe
   through HTTPS / Stripe API with the idempotency key.
10. Stripe returns the payment status and transaction reference, or the
    payment worker keeps the request queued for retry after temporary
    network or provider errors.
11. Payment Integration stores the final payment reference and payment
    status through Persistence.
12. The frontend shows the completed ride and the current payment status
    to customer and driver.
<br><br>

**Notable aspects:**

-   The scenario supports `REQ_9`, `REQ_10`, and `BG_2`.
-   Stripe is the only external payment system in the MVP.
-   The ride can be completed after the payment request is durably
    queued; the payment status can remain `pending` or `retrying` until
    the payment worker receives a final Stripe result.
-   Payment result, ride status, and reporting data are persisted to
    support ride history and commission reporting.
<br><br>

## Runtime Scenario 4: Customer Payment Fails

![Runtime Scenario 4](./resource/scenario4.png)

1. The payment worker reads a queued payment request from the internal
   durable payment queue and calls Stripe through HTTPS / Stripe API with
   the idempotency key.
2. Stripe rejects the transaction with a permanent payment error such as
   `card_declined`, `insufficient_funds`, or `expired_card`.
3. The payment worker classifies the result as non-retryable and stops
   retrying the queue message.
4. Payment Integration updates the payment status from `pending` to
   `failed` through Persistence and logs the failure context without
   exposing payment details or unnecessary personal data.
5. Administration & Reporting receives the failed payment information so
   the founding team can review the operational discrepancy.
6. The payment request is acknowledged and removed from the internal
   durable payment queue to prevent an infinite retry loop.
7. The frontend fetches the updated payment status through the Frontend
   REST API or receives it through an application update.
8. The customer sees that the ride was completed but payment failed, and
   the founding team can perform operational correction or restrict
   further bookings until the debt is cleared.
<br><br>

**Notable aspects:**

-   The scenario supports `REQ_9`, `REQ_10`, `REQ_11`, `BG_2`, and
    `QS_PRIV_1`.
-   The ride status remains `completed` even though the payment status is
    `failed`; this keeps the ride history and reporting data truthful.
-   Permanent payment failures are not retried endlessly, which protects
    the external provider integration and keeps operations observable.
-   Logs and reports must avoid sensitive payment data while still
    giving the team enough context to resolve the failed payment.
<br><br>

## Runtime Scenario 5: Stripe Payment Outage with Automatic Queue Retry

![Runtime Scenario 5](./resource/scenario5.png)

1. The payment worker reads a pending payment request from the internal
   durable payment queue.
2. The payment worker sends an HTTPS request to the Stripe API using the
   unique idempotency key.
3. Stripe does not respond because of a network timeout or provider
   outage.
4. The payment worker classifies the error as a temporary technical
   failure.
5. The payment request remains in the queue instead of being acknowledged
   as completed.
6. The queue retry policy delays the next attempt according to a defined
   backoff period.
7. After the backoff time expires, the payment worker reads the same
   payment request again.
8. The payment worker sends another HTTPS request to Stripe with the
   same idempotency key.
9. Stripe has recovered, processes the request without double-charging,
   and returns a successful transaction status and reference.
10. Payment Integration stores the final payment status and transaction
    reference through Persistence.
<br><br>

**Notable aspects:**


-   The scenario supports `REQ_9`, `REQ_10`, `BG_2`, and the mitigation
    of external provider dependency risks.
-   The ride remains `completed` while payment processing continues in
    the background, so the user workflow is not interrupted by temporary
    provider outages.
-   The idempotency key prevents duplicate charges when the same payment
    request is retried.
-   If Stripe remains unavailable beyond the standard retry window, the
    request can be escalated to operational review without corrupting
    ride or payment state.
<br><br>

# Deployment View

## Infrastructure Level 1

### Overview

The MVP is deployed with a cost-efficient three-environment setup:
development, test/staging, and production. Production initially runs on
one rented Linux server to satisfy the budget constraints `C_1` and
`C_2`. When demand grows, cloud services can be added later according to
constraint `C_3`.
<br><br>

| Environment | Infrastructure | Purpose |
|-------------|----------------|---------|
| Development | Local developer machines with local or test database instances. | Implementation, local testing, and debugging by the three entrepreneurs/developers. |
| Test / Staging | Separate staging setup on the rented Linux server, isolated from production by configuration, database/schema, ports, and access rules. | Integration testing, deployment rehearsal, and acceptance checks before production releases. |
| Production | Rented Linux server with Nginx, Java backend monolith, payment worker, internal payment message queue, MySQL database, and backup tooling. | Productive use by customers, drivers, founding team, and controlling. |
<br><br>

### Motivation

The deployment structure favors low cost and operational simplicity. All
productive server-side components run on one rented Linux server during
the MVP phase. Nginx acts as reverse proxy and TLS termination point.

The Java backend monolith and MySQL database run on the same server to
avoid additional infrastructure cost and complexity. The internal
payment message queue and payment worker also run on this server during
the MVP phase so payment requests survive temporary network or provider
failures. External authentication and Stripe remain outside the rented
server and are reached through HTTPS.

Customers and drivers use the mobile app. Internal administration and
controlling can be accessed through a browser-based frontend served by
the same frontend/application setup. This keeps the customer-facing
experience mobile-first while still allowing efficient internal work.
<br><br>

### Quality and Performance Features

| Concern | Infrastructure Measure |
|---------|------------------------|
| Cost efficiency | One rented Linux server hosts Nginx, backend, payment worker, payment queue, database, and staging/production setups for the MVP. |
| Security | Public access is routed through Nginx with TLS. Administrative access is restricted, e.g. via SSH and limited network access. |
| Reliability | Payment requests are stored in a durable internal queue before Stripe communication. Automated backups are created for the MySQL database, application configuration, and relevant deployment artifacts. |
| Recoverability | Backups are encrypted and copied to external off-server storage. Restore tests are performed regularly to verify that backups can actually be used. |
| Scalability path | If the rented server becomes insufficient, selected parts can later move to cloud services while the rented server remains available for backup purposes. |
<br><br>

### Mapping of Building Blocks to Infrastructure
<br>

![Deployment View](./resource/deployment-view.png)
<br><br>

| Building Block / Artifact | Deployment Target | Notes |
|---------------------------|-------------------|-------|
| youRide Mobile App / Frontend | Customer and driver mobile devices; internal browser clients for administration and controlling. | Mobile-first client for customers and drivers. Browser access is used for internal admin/reporting workflows. |
| Nginx Reverse Proxy | Rented Linux server | Handles HTTPS entry point, TLS termination, reverse proxying to the Java backend, and serving browser-based frontend assets if needed. |
| youRide Backend Monolith | Rented Linux server | Java application deployed as one backend artifact. |
| Payment Worker | Rented Linux server, initially as part of or next to the Java backend deployment | Processes queued payment requests, calls Stripe with retries and idempotency keys, and stores final payment status. |
| Internal Payment Message Queue | Rented Linux server | Durably stores payment requests and retry metadata until the payment worker can process them. |
| MySQL Database | Rented Linux server | Stores customer, driver, ride, payment reference, and reporting data. |
| Backup tooling | Rented Linux server plus external off-server backup storage | Creates encrypted database and configuration backups and copies them away from the production server. |
| External Auth Provider | External provider infrastructure | Used through HTTPS for authentication and identity management. |
| Stripe Payment Provider | Stripe infrastructure | Used through HTTPS / Stripe API by the payment worker for ride payments. |
<br><br>

## Infrastructure Level 2

### Production Linux Server

The production server is the central infrastructure element during the
MVP phase.
<br><br>

| Infrastructure Element | Responsibility |
|------------------------|----------------|
| Nginx | Public HTTPS endpoint, reverse proxy to the backend, TLS handling, optional serving of browser-based frontend assets. |
| Java Backend Monolith | Executes youRide business logic, REST API, WebSocket/TLS tracking endpoint, provider integrations, and internal module logic. |
| Payment Worker | Processes durable payment queue messages, calls Stripe with retry and idempotency handling, and writes final payment status. |
| Internal Payment Message Queue | Durable local queue for payment requests that must survive temporary network, provider, or backend processing errors. |
| MySQL Database | Persistent storage for application data. |
| Backup tooling | Automated encrypted backups of database, configuration, and deployment-relevant files. |
| Monitoring / operational scripts | Basic health checks, log inspection, deployment support, and operational maintenance by the DevOps/network employee. |
<br><br>

### Backup Storage

Backups must not only remain on the production server. The standard
backup approach for this setup is:

1. Create automated MySQL backups and configuration backups.
2. Encrypt backup artifacts.
3. Store a local short-term copy for quick recovery.
4. Copy backups to external off-server storage.
5. Keep a simple retention policy, for example daily backups for recent
   recovery and weekly backups for longer recovery windows.
6. Perform regular restore tests.

This keeps the setup affordable while reducing the risk that a server
failure also destroys all backups.
<br><br>

# Cross-cutting Concepts

The following concepts are documented because they affect several
building blocks and support the main quality goals and constraints.
<br><br>

## Modular Monolith Boundaries

The backend is deployed as one Java application, but internally
structured into modules with clear responsibilities. This supports fast
implementation and simple deployment while reducing the risk that the
monolith becomes hard to maintain.
<br><br>

| Rule | Explanation |
|------|-------------|
| Keep business capabilities separated | Identity/Auth Integration, Customer Management, Driver Management & Verification, Ride Management & Matching, Live Tracking, Payment Integration, Administration & Reporting, and Persistence are treated as separate modules. |
| Use explicit module interfaces | Modules communicate through internal Java service interfaces instead of directly depending on implementation details. |
| Keep persistence access isolated | Database access is routed through the Persistence module or repository interfaces, so business logic does not depend directly on MySQL details. |
| Avoid unnecessary shared logic | Shared code is only introduced when it represents stable common behavior. This helps keep module cohesion high. |

Affected building blocks: youRide Backend Monolith, Persistence, MySQL
Database.
<br><br>

## Security and Authentication

Authentication is delegated to an external authentication provider
because identity management is security-critical and costly to implement
from scratch. The backend validates authentication tokens before
executing protected operations.
<br><br>

| Rule | Explanation |
|------|-------------|
| Protect all backend APIs | REST endpoints and WebSocket/TLS tracking endpoints require authenticated users where user-specific data is involved. |
| Use role-based access | Customer, driver, admin, and controlling functions are separated by roles. |
| Keep authentication external | Registration, login, and token issuing are handled by the external authentication provider. |
| Use TLS for public communication | Public frontend-backend communication and provider communication use encrypted channels. |
| Test security-critical paths | Authentication, authorization, payment handoff, and admin access are covered by penetration tests. |
<br>

Affected building blocks: Mobile App / Frontend, Backend Monolith,
External Auth Provider, Internal Payment Message Queue, Payment Worker,
Stripe Payment Provider, Nginx.
<br><br>

## Testing Concept

Testing is treated as a cross-cutting concept because the main MVP risks
span several modules and external providers: price calculation, driver
matching, ride status changes, live tracking, authentication, payment
handoff, deployment, and backup recovery.
<br><br>

| Rule | Explanation |
|------|-------------|
| Cover core business rules with unit tests | Price calculation, matching decisions, ride status transitions, cancellation rules, and payment reference handling are tested close to the backend modules that implement them. |
| Verify module collaboration with integration tests | Backend module interfaces, persistence access, REST APIs, and WebSocket/TLS tracking flows are tested together so that the modular monolith behaves consistently as one application. |
| Test external provider integration explicitly | Authentication and Stripe integration are tested with provider test modes, mocks, or contract-style checks so that the MVP does not depend on manual production checks. |
| Use staging for end-to-end and acceptance checks | The separate staging/test environment is used for complete customer, driver, admin, payment, and deployment rehearsal workflows before production releases. |
| Include security and privacy tests | Authentication, authorization, admin access, payment handoff, logging, and handling of personal data are checked as part of regression and penetration testing. |
| Validate operational recovery | Backups are not only created, but also verified through restore tests so that database and configuration recovery remain reliable. |
<br>

Affected building blocks: Mobile App / Frontend, Backend Monolith,
Identity/Auth Integration, Ride Management & Matching, Live Tracking,
Payment Integration, Internal Payment Message Queue, Payment Worker,
Persistence, MySQL Database, External Auth Provider, Stripe Payment
Provider, Backup tooling, Deployment infrastructure.
<br><br>

## GDPR-oriented Data Governance

youRide handles personal data such as customer profiles, driver
profiles, live location data, ride history, and payment references.
Therefore, data handling follows GDPR-oriented principles from the
beginning.
<br><br>

| Rule | Explanation |
|------|-------------|
| Data minimization | Store only data that is needed for registration, rides, payment references, verification, history, reporting, and legal obligations. |
| Purpose limitation | Use personal data only for the ride sharing service, payment processing, administration, reporting, and required operational purposes. |
| Access control | Customer, driver, admin, and controlling access to data is restricted by role. |
| Retention and deletion | Retention rules must be defined for profiles, ride history, location data, and payment references. Data that is no longer needed must be deleted or anonymized. |
| Backup awareness | Backups contain personal data and must therefore be encrypted and handled according to the same governance principles. |
<br>

Affected building blocks: Backend Monolith, MySQL Database, Backup
tooling, Administration & Reporting, External Auth Provider, Internal
Payment Message Queue, Payment Worker, Stripe Payment Provider.
<br><br>

## Error Handling and Logging

Errors can occur in user workflows, backend modules, database access,
authentication, payment processing, and deployment operations. A
consistent error handling and logging concept is needed so that the small
team can detect and fix problems quickly.
<br><br>

| Rule | Explanation |
|------|-------------|
| Separate business and technical errors | Business errors, such as unavailable drivers or invalid ride status transitions, are handled differently from technical failures, such as database or provider errors. |
| Return user-friendly messages | Customers and drivers receive understandable error messages without exposing internal implementation details. |
| Log operationally relevant events | Authentication failures, payment failures, ride status changes, matching problems, and backend exceptions are logged with enough context for troubleshooting. |
| Avoid sensitive data in logs | Logs must not contain unnecessary personal data, payment details, secrets, or authentication tokens. |
| Support monitoring | Logs and health checks support the DevOps/network employee in detecting production issues. |
<br>

Affected building blocks: Backend Monolith, Nginx, MySQL Database,
External Auth Provider, Internal Payment Message Queue, Payment Worker,
Stripe Payment Provider.
<br><br>

# Architecture Decisions

The following decisions document the most important architecture choices
that stakeholders must be able to understand and trace.
<br><br>

| ID | Status | Problem / Context | Considered Alternatives | Decision and Rationale | Consequences |
|----|--------|-------------------|-------------------------|------------------------|--------------|
| AD_1 | Accepted | The startup needs a fast, cost-efficient MVP that can be built and operated by a small team. | Microservices, service-oriented architecture. | Use a modular monolith. It keeps deployment simple and affordable while internal module boundaries support maintainability. | Positive: fast implementation and simple operation. Negative: scaling individual business capabilities independently is not possible in the MVP architecture. |
| AD_2 | Accepted | Initial infrastructure costs must stay low, but the system still needs a growth path. | Cloud-first deployment, fully on-premises infrastructure. | Start on a rented Linux server and add cloud services later when scaling becomes necessary. The rented server remains available for backup purposes. | Positive: low initial cost and simple operations. Negative: the rented server can become a bottleneck and requires a later migration path. |
| AD_3 | Accepted | Authentication is security-critical and too expensive to implement safely from scratch in the MVP phase. | Self-built authentication, external authentication provider. | Use an external authentication provider for registration, login, and token issuing. | Positive: reduced security and implementation risk. Negative: dependency on an external provider and its availability, pricing, and integration API. |
| AD_4 | Accepted | Payment processing is business-critical, security-critical, and legally sensitive. | Self-built payment handling, another payment provider. | Use Stripe as external payment provider for ride payments and transaction references. | Positive: faster implementation and proven payment handling. Negative: dependency on Stripe fees, API availability, and provider rules. |
| AD_5 | Accepted | The MVP needs map display, geocoding, reverse geocoding, route distance, and ETA information for ride search, booking, pricing support, and live tracking usability. Building and operating this data stack would be too expensive for the startup. | Self-hosted maps/routing stack, no external maps provider in the MVP, direct provider coupling from domain logic. | Use an external maps/geocoding/routing provider through a replaceable integration. Keep ride matching and price calculation rules inside youRide and send only the location data required for provider requests. | Positive: faster MVP delivery and familiar map/ETA usability. Negative: dependency on provider availability, pricing, API stability, usage limits, terms, and privacy handling for location data. |
<br><br>

# Quality Requirements

## Quality Tree

The following overview summarizes the relevant quality requirements for
the MVP. The top three quality goals are already introduced in chapter 1
and are refined here with concrete scenarios.
<br><br>

| Quality Category | Priority | Related Goals / Constraints | Refinement | Related Scenarios |
|------------------|----------|-----------------------------|------------|-------------------|
| Functional suitability | High | `QG_1` | Correct price calculation, automatic driver matching, consistent ride status, and correct ride/payment data. | `QS_FUNC_1`, `QS_FUNC_2` |
| Usability | High | `QG_2` | Customers and drivers can use the core ride workflow without external instructions. | `QS_USAB_1` |
| Scalability | High | `QG_3`, `C_3` | The system can handle a growing customer base and has a planned path from rented server to cloud services. | `QS_SCAL_1` |
| Performance efficiency | Medium | `QG_1`, `QG_2` | Important user actions should respond in less than two seconds under normal MVP load. | `QS_PERF_1` |
| Live tracking timeliness | Medium | `REQ_7` | Live GPS updates should be visible frequently enough to make active rides transparent. | `QS_PERF_2` |
| Security and GDPR | High | `C_5` | Sensitive customer, driver, location, ride, and payment reference data must be protected. | `QS_SEC_1`, `QS_PRIV_1` |
<br><br>

![Quality Tree](./resource/quality_tree.png)
<br><br>

## Quality Scenarios

The following quality scenarios make the most important quality
requirements concrete and measurable enough for discussion and
evaluation.
<br><br>

| ID | Type | Quality Attribute | Source / Stimulus | Environment | Affected Artifact | Response | Response Measure |
|----|------|-------------------|-------------------|-------------|-------------------|----------|------------------|
| QS_FUNC_1 | Usage scenario | Functional suitability | A customer books a ride with pickup, destination, and desired time. | Normal MVP operation. | Ride Management & Matching, Persistence, Mobile App / Frontend. | The system calculates the price, matches a suitable verified driver, stores the ride, and returns the booking result. | Ride data, calculated price, selected driver, and initial status are stored consistently and shown to the customer. |
| QS_FUNC_2 | Usage scenario | Functional suitability | A ride status changes from requested to accepted, in progress, completed, or cancelled. | Normal MVP operation. | Ride Management & Matching, Live Tracking, Persistence, Mobile App / Frontend. | The status change is validated, persisted, and shown consistently to customer and driver. | Customer and driver see the same ride status after the update. |
| QS_USAB_1 | Usage scenario | Usability | A new customer wants to book a ride without reading external documentation. | First-time usage on the mobile app. | Mobile App / Frontend, Backend Monolith. | The customer can register, search for a ride, see the calculated price, and request the ride through an intuitive workflow. | The customer can complete the booking workflow without external instructions. |
| QS_SCAL_1 | Change scenario | Scalability | The customer base and ride volume grow beyond what the rented Linux server can comfortably handle. | Growth phase after MVP validation. | Deployment infrastructure, Backend Monolith, MySQL Database, Backup tooling. | Selected infrastructure parts can be moved to cloud services while the rented server remains available for backup. | Migration planning can reuse the existing deployment, data export, and backup concepts without redesigning the whole system. |
| QS_PERF_1 | Usage scenario | Performance efficiency | A customer searches for or books a ride. | Normal MVP operation. | Mobile App / Frontend, Backend Monolith, MySQL Database. | The system processes the request and returns the result. | Search and booking responses should complete in less than 2 seconds under normal MVP load. |
| QS_PERF_2 | Usage scenario | Live tracking timeliness | A driver sends live GPS updates during an active ride. | Active ride with WebSocket/TLS connection. | Live Tracking, Mobile App / Frontend, Backend Monolith. | The backend receives and distributes live location and ride status updates. | Location updates should be sent approximately every 2-3 seconds during an active ride. |
| QS_SEC_1 | Usage scenario | Security | An unauthenticated or wrongly authorized user calls a protected backend endpoint. | Normal operation. | Backend Monolith, External Auth Provider, Nginx. | The request is rejected and no protected data is returned. | Protected REST and WebSocket/TLS endpoints require valid authentication and role-based authorization. |
| QS_PRIV_1 | Usage scenario | GDPR / privacy | A user profile, ride, location, payment reference, log entry, or backup contains personal data. | Normal operation and backup operation. | Backend Monolith, MySQL Database, Backup tooling, Logs. | Personal data is stored only for defined purposes, access is role-restricted, and sensitive data is not unnecessarily written to logs. | Data handling follows the GDPR-oriented governance rules from chapter 8 and backups are encrypted. |
<br><br>

# Risks and Technical Debts

The following risks and technical debts must be monitored during
development and operation.
<br><br>

| Priority | Risk / Technical Debt | Related Decisions / Constraints | Description | Suggested Measure |
|----------|-----------------------|---------------------------------|-------------|-------------------|
| 1 | Monolith can become too complex | `AD_1`, `QG_3` | The modular monolith is good for the MVP, but the code base can become difficult to understand and change when features, modules, and dependencies grow. | Keep module boundaries explicit, review dependencies regularly, and avoid direct access to implementation details of other modules. |
| 2 | Single rented server as bottleneck and single point of failure | `AD_2`, `C_1`, `C_3` | Backend, MySQL, Nginx, and production infrastructure run on one rented Linux server during the MVP. A server failure or overload can affect the whole system. | Monitor server resources, define migration triggers, keep backups off-server, and prepare a later cloud scaling path. |
| 3 | External provider dependency for authentication, payment, and maps/routing | `AD_3`, `AD_4`, `AD_5` | youRide depends on the availability, pricing, API stability, usage limits, and terms of the external authentication provider, Stripe, and the maps/geocoding/routing provider. Provider problems can block login, delay final payment confirmation, or degrade route/ETA/geocoding-dependent booking and tracking views. | Keep provider integrations isolated, queue payment requests durably, use retries and idempotency keys where appropriate, log provider failures, monitor provider usage and costs, document fallback procedures, and store external transaction references consistently. |
| 4 | GDPR and privacy risk for sensitive data | `C_5`, `QS_PRIV_1` | Customer profiles, driver profiles, location data, ride history, and payment references are sensitive personal data. Wrong access, excessive storage, or unsafe logs can create legal and trust problems. | Apply GDPR-oriented data governance, role-based access control, data minimization, retention/deletion rules, encrypted backups, and logging rules that avoid sensitive data. |
| 5 | Monolithic architecture is limited for scaling | `AD_1`, `QG_3`, `QS_SCAL_1` | A monolith cannot scale individual business capabilities independently. If only live tracking, matching, or payment integration becomes heavily loaded, the whole backend must initially be scaled together. | Keep the monolith modular, monitor load by module or use case where possible, and use the cloud migration path when the rented server is no longer sufficient. |
| 6 | Backup and restore risk | `C_3`, Deployment View | Backups are only useful if they are complete, encrypted, stored away from the production server, and restorable. Without restore tests, the team might discover backup problems too late. | Automate database and configuration backups, copy them to off-server storage, define retention, and perform regular restore tests. |
<br><br>

# Glossary

| Term | Definition |
|------|------------|
| Administration & Reporting | Backend module that supports founder/admin operations, driver verification decisions, ride inspection, revenue data, commission reporting, and cost overview. |
| Admin / Administrator | Internal user who can review users, verify drivers, inspect rides, and perform operational corrections. |
| Angular | Frontend technology chosen for the youRide mobile app/frontend layer. |
| Architecture Decision (`AD_*`) | Documented architectural choice with context, alternatives, rationale, and consequences. |
| Authentication Token | Digital proof issued by the external authentication provider and validated by the backend before protected operations are executed. |
| Automatic Driver Matching | youRide functionality that selects a suitable verified and available driver for a customer ride request. |
| Backup | Copy of database, configuration, and deployment-relevant files used for recovery after data loss or server failure. |
| Backend | Server-side part of youRide that provides business logic, APIs, integration with external providers, persistence access, and operational functionality. |
| Backend Monolith | Java backend application that contains the central youRide business logic and is deployed as one artifact. |
| Booking | Customer action that requests a ride and triggers price calculation, driver matching, and ride creation. |
| Browser-based Frontend | Internal web access used by administration and controlling for operational workflows. |
| Cloud Services | External infrastructure services that can be added later when the rented Linux server is no longer sufficient for growth. |
| Commission | Revenue model in which youRide receives a share of each completed ride payment. |
| Constraint (`C_*`) | Requirement or restriction that limits architectural freedom, for example budget, GDPR, team size, or rented server operation. |
| Controlling Employee | Internal stakeholder responsible for cost overview, revenue data, commission reporting, and basic operational reporting. |
| Customer | User who searches for, books, tracks, cancels, and completes rides through youRide. |
| Customer Management | Backend module that manages customer profile data and customer-specific ride access. |
| Data Governance | Rules for handling data, especially personal data, including purpose, access, retention, deletion, logging, and backup handling. |
| Development Environment | Local environment on developer machines used for implementation, testing, and debugging. |
| DevOps / Network Employee | Internal employee responsible for reliable deployment, server/network operation, monitoring, backups, and later cloud integration. |
| Driver | Private or professional user who offers rides, accepts or declines ride requests, sends live location data, and completes rides. |
| Driver Management & Verification | Backend module that manages driver profile data, availability, and verification status. |
| Driver Verification | Process in which the company checks and approves a driver before the driver can offer rides on the platform. |
| External Auth Provider | External service used for registration, login, authentication, and token issuing. |
| Frontend | User-facing part of youRide that presents customer, driver, administration, and controlling workflows and communicates with the backend through APIs. |
| Frontend REST API | HTTPS/REST interface used by the mobile app/frontend for normal application commands such as search, booking, cancellation, completion, history, administration, and reporting. |
| GDPR | General Data Protection Regulation; legal basis for youRide's privacy and data governance decisions. |
| GDPR-oriented Data Governance | youRide concept that applies GDPR principles to customer, driver, ride, location, payment reference, log, and backup data. |
| HTTPS | Encrypted HTTP communication used for frontend-backend communication and external provider APIs. |
| Identity / Auth Integration | Backend module that integrates with the external authentication provider and maps authenticated users to roles. |
| Internal Payment Message Queue | Durable internal queue that stores payment requests, retry metadata, and idempotency information before the payment worker communicates with Stripe. |
| Integration Test | Test that checks whether several system parts or external integrations work together correctly. |
| Java | Programming language chosen for the youRide backend monolith. |
| Live GPS | Regular location information sent by the driver during an active ride. |
| Live Tracking | youRide functionality that distributes active ride status and live GPS updates between driver and customer. |
| Live Tracking Channel | WebSocket/TLS channel used for active ride location and status updates. |
| Mobile App / Frontend | User-facing application for customers and drivers; internal browser access can be used for administration and controlling. |
| Modular Monolith | Architectural style where the backend is deployed as one application but internally structured into explicit modules. |
| Module | Internal backend building block with a clear responsibility and explicit interfaces. |
| MVP | Minimum Viable Product; the first useful version of youRide built to validate the Austrian market with low cost and fast delivery. |
| MySQL Database | Relational database used by youRide to store profiles, rides, statuses, prices, payment references, and reporting data. |
| Nginx | Reverse proxy on the rented Linux server that handles HTTPS entry, TLS termination, reverse proxying, and optional frontend asset delivery. |
| OAuth 2.0 / OpenID Connect | Standard authentication and authorization protocols assumed for the external authentication provider integration. |
| Off-server Backup Storage | Backup storage outside the production server, used so that a server failure does not destroy all backups. |
| Payment Integration | Backend module that creates durable payment requests, coordinates queued payment processing, stores payment references, and handles payment status. |
| Payment Queue | Short name for the internal payment message queue used for reliable queued payment handoff. |
| Payment Reference | Stored reference to an external payment transaction, used for ride history, reconciliation, and reporting. |
| Payment Status | State of a payment, for example `pending`, `retrying`, `confirmed`, or `failed`, tracked separately from the ride status. |
| Payment Worker | Background component that consumes queued payment requests, calls Stripe with retries and idempotency keys, and stores the resulting payment status and transaction reference. |
| Penetration Test | Security test that attempts to find vulnerabilities in authentication, authorization, payment handoff, admin access, and other security-critical paths. |
| Persistence | Backend module or repository layer that isolates database access from business logic. |
| Production Environment | Productive infrastructure used by real customers, drivers, founding team, and controlling. |
| Quality Goal (`QG_*`) | High-priority architectural quality target such as functional suitability, usability, or scalability. |
| Quality Scenario (`QS_*`) | Concrete and evaluable scenario that describes how the system must react to a stimulus. |
| Rented Linux Server | Initial production server used to keep infrastructure costs low during the MVP phase. |
| REST API | Request/response interface used for normal application operations between frontend and backend. |
| Restore Test | Test that verifies whether a backup can actually be restored and used after a failure. |
| Ride | Central business object representing a trip request and execution, including customer, driver, pickup, destination, price, payment reference, and status. |
| Ride History | Stored list of completed or cancelled rides visible to customers and drivers and useful for reporting. |
| Ride Management & Matching | Backend module that handles ride requests, matching, prices, status transitions, cancellation, completion, and ride history. |
| Ride Request | Customer request for a spontaneous or planned ride. |
| Ride Status | Lifecycle state of a ride, for example `requested`, `accepted`, `in progress`, `completed`, or `cancelled`. |
| Role-based Access Control | Access concept where customer, driver, admin, and controlling functions are restricted by role. |
| Staging / Test Environment | Environment used for integration testing, deployment rehearsal, and acceptance checks before production releases. |
| Stripe | External payment provider used by youRide for ride payments and transaction references. |
| TLS | Encryption protocol used for secure HTTPS and WebSocket communication. |
| Unit Test | Test that verifies a small unit of code, such as price calculation or ride status logic, in isolation. |
| WebSocket | Bidirectional communication channel used for live tracking updates during active rides. |
| youRide | Austrian ride sharing startup application documented in this architecture documentation. |
