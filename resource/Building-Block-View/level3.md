@startuml
title Ride Management & Matching (Level 3 White Box)

allowmixing

skinparam componentStyle rectangle
left to right direction

' External Inbound Interface
() "Ride Actions" as Actions

' Outer Component Boundary Block
component "Ride Management & Matching" as RideComponent #LightYellow {

    class RideApplicationService {
        
repository :RideRepositoryPort
matchingService :MatchingService
    
statusPolicy :RideStatusPolicy+ searchRides(pickup, destination) : RideOption[]+ requestRide(customerId, pickup, destination) : UUID+ acceptRide(rideId, driverId) : void+ completeRide(rideId) : void
}

class Ride <<Aggregate Root>> {
    
id :UUID
customerId :UUID
driverId :UUID
paymentReference :String
paymentStatus :PaymentStatus
status :RideStatus
+ assignDriver(driverId) : void+ accept() : void+ complete() : void
}

class Location <<Value Object>> {
    + latitude :double
    + longitude :double
}

class Money <<Value Object>> {
    + amount :decimal
    + currency :String
}

class MatchingService {
    + matchDriver(pickup, time) : UUID
}

class RideStatusPolicy {+ canAccept(ride, driverId) : boolean+ canComplete(ride) : boolean}
}

' External Outbound Target Component
component "Persistence Module" as Persistence #LightGray

' --- Connections ---

' Inbound port routing
Actions ..> RideApplicationService : "execute-command"

' Internal orchestration links
RideApplicationService --> MatchingService
RideApplicationService --> RideStatusPolicy
RideApplicationService ..> Ride : "orchestrates"

' Multiplicity compositions
Ride "1" -- "2" Location : "pickup / destination"
Ride "1"-- "1" Money : "calculated price"

' Outbound boundary path
RideComponent ..> Persistence : "Save / Load \n(RideRepositoryPort)"
@enduml