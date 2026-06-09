# youRide Architecture Decision Notes

This document is a decision-oriented companion to the arc42 architecture
documentation. It explains why the architecture is shaped the way it is,
which alternatives were considered, and what consequences the team accepts
for the MVP.

The main architecture documentation remains `arc42-template-EN.md`.
This file focuses on architectural reasoning and traceability.

## Chapter 1 - Introduction and Goals

The architecture is driven by the business goal of launching a useful
ride sharing MVP quickly in Austria while keeping costs low. youRide is
not designed as a large platform from day one. It is designed as a
startup system that must validate the market, gain paying customers and
drivers, and still leave room for later growth.

The important decision in this chapter is to prioritize a lean, useful
MVP over a maximally flexible enterprise architecture. The MVP includes
registration, ride search, booking, automatic driver matching, ride
acceptance, live tracking, cancellation, ride completion, payment, ride
history, driver verification, administration, and reporting.

The top quality goals are functional suitability, usability, and
scalability. Functional suitability is the strongest driver because wrong
prices, wrong driver assignments, inconsistent ride states, or unreliable
payment handling would directly damage trust. Usability is next because
customers and drivers must be able to use the service without
documentation. Scalability matters, but it is intentionally balanced
against the cost constraints of the startup phase.

## Chapter 2 - Architecture Constraints

The most important constraint is cost. The production system starts on a
rented Linux server instead of a cloud-first setup. This limits initial
operating cost and makes the system understandable for the small team,
but it also introduces a bottleneck and a single point of failure.

The technology stack is not externally mandated. Java, Angular, MySQL,
REST, WebSocket/TLS, Nginx, an internal payment queue, Stripe, an
external authentication provider, and an external maps/geocoding/routing
provider are architecture choices. They are decisions, not constraints.

GDPR is a real constraint. Customer profiles, driver profiles, ride
history, payment references, and live location data are personal data.
That means privacy, retention, access control, logging, and backups must
be considered from the beginning.

The team size is also a constraint. Three founders/developers, one
DevOps/network employee, and one controlling employee cannot operate a
large distributed system safely in the MVP phase. This strongly supports
the modular monolith and simple deployment choices.

## Chapter 3 - System Scope and Context

The system boundary includes the mobile app/frontend, the Java backend
monolith, the MySQL database, the internal payment queue, the payment
worker, and the admin/reporting functions. Customers, drivers, the
founding team, the controlling employee, the external authentication
provider, Stripe, and the external maps/geocoding/routing provider are
outside or at the edge of that boundary.

The key decision is to keep ride matching, price calculation, live
tracking state, administration, and reporting as youRide functionality.
External providers supply technical capabilities only:

- the authentication provider handles login, registration, token issuing,
  and identity validation
- Stripe handles payment execution and transaction references
- the maps/geocoding/routing provider supplies map display data, route
  distance, ETA, geocoding, and reverse geocoding data

This separation protects the domain model. youRide owns the ride
workflow and business rules, while replaceable providers support
security-critical or data-heavy capabilities that are too expensive to
build for the MVP.

## Chapter 4 - Solution Strategy

The central strategy is to build a modular monolith. The backend is
deployed as one Java application, but internally divided into explicit
modules such as Identity/Auth Integration, Customer Management, Driver
Management & Verification, Ride Management & Matching, Live Tracking,
Payment Integration, Administration & Reporting, and Persistence.

The team accepts the tradeoff that individual business capabilities
cannot initially be scaled independently. In return, the startup gains
simple deployment, lower operating cost, faster implementation, and less
operational complexity.

Authentication, payment, and maps/routing are delegated to external
providers. This is not done because these topics are unimportant. It is
done because they are too expensive, risky, or data-heavy to build well
from scratch in the MVP. The architecture therefore isolates provider
integrations and keeps core business rules inside youRide.

Reliability is addressed through targeted testing and through the
internal durable payment queue. Payment execution is asynchronous because
a temporary Stripe outage must not lose payment requests or corrupt ride
state.

## Chapter 5 - Building Block View

The building block view follows the modular monolith decision. Level 1
shows the mobile app/frontend, backend monolith, database, internal
payment queue, payment worker, and external providers. Level 2 refines
the backend into business-oriented modules. Level 3 refines Ride
Management & Matching because it is the core domain module and the main
source of complexity.

The important design choice is that business capabilities are separated
by responsibility, not by deployment unit. The monolith is one
deployable artifact, but it should not become one unstructured codebase.

Ride Management & Matching is kept cohesive around ride lifecycle,
matching, pricing, status transitions, cancellation, completion, and
history. Payment details are delegated to Payment Integration and the
payment worker. Persistence is accessed through repository-style ports so
business logic does not depend directly on MySQL details.

This chapter also makes the future migration path clearer. If growth
requires cloud services or later service extraction, the module
boundaries provide candidate seams for scaling or separation.

## Chapter 6 - Runtime View

The runtime view documents the workflows that are most important for
understanding the architecture at runtime. The selected scenarios cover
successful ride booking, driver acceptance with live tracking, ride
completion with payment processing, permanent payment failure, and Stripe
outage with retry.

The decision behind these scenarios is to show both happy paths and
critical failure paths. A ride sharing system is not reliable merely
because booking works. It must also handle provider failures, payment
errors, status consistency, and retry behavior in a controlled way.

The payment scenarios are especially important. A completed physical ride
remains completed even if the payment status is still pending, failed, or
retrying. Ride status and payment status are therefore separated. This
keeps the ride history truthful and allows the team to resolve failed or
delayed payments operationally.

The queue-based payment flow is an explicit reliability decision. It
protects payment requests from temporary network errors and provider
outages. Idempotency keys prevent duplicate charges when the same queued
payment request is retried.

## Chapter 7 - Deployment View

The MVP deployment starts with a rented Linux server. Nginx, the Java
backend monolith, the payment worker, the internal payment queue, MySQL,
monitoring scripts, and backup tooling run on or around that server.
Customers and drivers use the mobile app; internal users can access
administration and reporting through browser-based frontend access.

This deployment decision is intentionally conservative. A cloud-first
architecture would offer more managed scalability, but it would increase
cost and operational complexity before the product has validated the
market.

The accepted downside is that the rented server can become a bottleneck
or single point of failure. The mitigation is to monitor the system,
define migration triggers, keep backups off-server, and preserve a later
path toward cloud services when growth justifies it.

Backups are part of the architecture decision, not an operational
afterthought. Database and configuration backups must be encrypted,
copied away from the production server, retained according to a simple
policy, and tested through restore exercises.

## Chapter 8 - Cross-cutting Concepts

The cross-cutting concepts turn the main decisions into implementation
rules. The most important ones are modular monolith boundaries, security
and authentication, testing, GDPR-oriented data governance, and error
handling/logging.

The modular monolith concept requires explicit module interfaces and
isolated persistence access. This keeps the codebase maintainable even
though the backend is deployed as one artifact.

The security concept delegates authentication to an external provider,
requires protected REST and WebSocket/TLS endpoints, uses role-based
access, and keeps public communication encrypted.

The data governance concept is driven by GDPR. The system should store
only necessary data, restrict access by role, define retention/deletion
rules, avoid sensitive logs, and handle backups as personal-data
containers.

The testing concept follows the risk profile. Price calculation,
matching, status transitions, payment handoff, persistence, provider
integrations, security, privacy, and backup recovery need explicit test
attention.

## Chapter 9 - Architecture Decisions

The current accepted architecture decisions are:

| ID | Decision | Main Reason | Accepted Consequence |
|----|----------|-------------|----------------------|
| AD_1 | Use a modular monolith. | Fast and cost-efficient MVP delivery by a small team. | Individual capabilities cannot be scaled independently at first. |
| AD_2 | Start on a rented Linux server and add cloud services later. | Keep initial infrastructure cost low while preserving a growth path. | The first deployment has bottleneck and availability risks. |
| AD_3 | Use an external authentication provider. | Authentication is security-critical and expensive to implement safely. | youRide depends on provider availability, API stability, pricing, and terms. |
| AD_4 | Use Stripe for payment execution. | Payment is business-critical, security-critical, and legally sensitive. | youRide depends on Stripe, but queueing and idempotency reduce failure impact. |
| AD_5 | Use an external maps/geocoding/routing provider through a replaceable integration. | Map display, route distance, ETA, and geocoding are too costly to build well for the MVP. | youRide depends on provider pricing, API stability, usage limits, and privacy handling. |

These decisions are connected. The modular monolith and rented-server
deployment keep the MVP affordable. External providers reduce
implementation risk. The queue-based payment design compensates for the
fact that external payment execution can fail or be temporarily
unavailable.

## Chapter 10 - Quality Requirements

The quality requirements explain what the decisions must achieve.
Functional suitability requires correct ride creation, matching, pricing,
status transitions, payment references, and persisted history. Usability
requires a booking flow that customers and drivers can complete without
external instructions. Scalability requires a path from the initial
rented server to later cloud services.

Performance is defined pragmatically: search and booking should respond
in less than two seconds under normal MVP load. Live tracking should send
updates approximately every two to three seconds during an active ride.

Security and GDPR requirements influence almost every chapter. Protected
APIs, role-based authorization, external authentication, TLS, data
minimization, careful logging, and encrypted backups are not optional
add-ons. They are part of the architecture baseline.

## Chapter 11 - Risks and Technical Debts

The main risks are accepted consciously:

| Priority | Risk | Why It Exists | Main Mitigation |
|----------|------|---------------|-----------------|
| 1 | Monolith complexity | The MVP starts as one backend artifact. | Keep modules explicit and review dependencies. |
| 2 | Rented server bottleneck and single point of failure | Backend, database, queue, worker, and reverse proxy initially share one server. | Monitor load, keep off-server backups, and prepare cloud migration triggers. |
| 3 | External provider dependency | Authentication, payment, and maps/routing depend on third parties. | Isolate integrations, monitor costs and failures, use retries and idempotency where appropriate. |
| 4 | GDPR and privacy risk | Customer, driver, ride, location, payment, log, and backup data are sensitive. | Apply role-based access, minimization, retention rules, encrypted backups, and safe logging. |
| 5 | Limited independent scaling | A monolith cannot scale one business capability alone. | Keep module boundaries clean and use the cloud migration path when necessary. |
| 6 | Backup and restore risk | Backups are only valuable if they can be restored. | Automate encrypted backups and perform regular restore tests. |

The payment failure and outage scenarios in the runtime view directly
address risk 3. They show how failed payments, temporary Stripe outages,
queue retries, idempotency keys, and operational review fit together.

## Chapter 12 - Glossary

The glossary is a small but important architecture decision. The team
uses terms such as Ride Status, Payment Status, Payment Queue, Payment
Worker, Live Tracking, External Auth Provider, and GDPR-oriented Data
Governance consistently across the documentation.

This reduces ambiguity when discussing requirements, building blocks,
runtime scenarios, risks, and future implementation tasks. For example,
separating Ride Status from Payment Status is not only terminology. It
is a design decision that allows a ride to be completed while payment is
still pending, failed, or retrying.

## Reading Order

For a quick understanding of the architecture reasoning, read these
sections first:

1. Chapter 1 for goals and quality drivers.
2. Chapter 4 for the main strategy.
3. Chapter 9 for accepted architecture decisions.
4. Chapter 6 for critical runtime behavior.
5. Chapter 11 for risks and mitigations.

The other chapters provide the context needed to understand why these
decisions are reasonable for the MVP.
