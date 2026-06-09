@startuml
title Component Context View - youRide (Business Context)

skinparam componentStyle rectangle
left to right direction

' Communication Partners: Users
actor "Customers" as Customer
actor "Drivers" as Driver
actor "Founding Team\n(Admins)" as Admin
actor "Controlling\nEmployee" as Control

' System under observation acting as a Black Box
component "youRide System\n(MVP)" as youRide #LightYellow

' Communication Partners: External Services/Systems
component "External Auth\nProvider" as Auth #LightGray
component "Stripe Payment\nProvider" as Stripe #LightGray
component "External Maps\nProvider" as Maps #LightGray

' Domain specific input/output and directions
Customer --> youRide : "Ride bookings &\nLive location"
youRide --> Customer : "Available rides,\nRide status & Prices"

Driver --> youRide : "Verification data &\nLive location"
youRide --> Driver : "Ride requests &\nCustomer details"

Admin --> youRide : "Driver verification\ndecisions"
youRide --> Admin : "User & ride overviews"

Control --> youRide : "Reporting requests"
youRide --> Control : "Commission, cost &\nrevenue data"

' Core external system boundaries
youRide --> Auth : "Identity verification\nrequests"
Auth --> youRide : "Authentication tokens"

youRide --> Stripe : "Payment requests\n(Ride commissions)"
Stripe --> youRide : "Payment status &\nTransaction references"

youRide --> Maps : "Pickup and destination addresses/coordinates\n& minimum location data"
Maps --> youRide : "Route, distance, ETA, geocoding,\nreverse geocoding & map display data"
@enduml