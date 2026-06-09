@startuml
title Ride Management & Matching (Level 3 White Box - Extended)
allowmixing
skinparam componentStyle rectangle
left to right direction

' External Inbound Interface
() "Ride Actions" as Actions

' Outer Component Boundary Block
component "Ride Management & Matching" as RideComponent #LightYellow {

    class RideApplicationService {
        - repository : RideRepositoryPort
        - matchingService : MatchingService
        - statusPolicy : RideStatusPolicy
        - routeEtaPort : RouteEtaPort
        - priceCalcService : PriceCalculationService
        
        + searchRides(pickup, destination) : RideOption[]
        + requestRide(customerId, pickup, destination) : UUID
        + acceptRide(rideId, driverId) : void
        + completeRide(rideId) : void
    }

    class Ride <<Entity>> {
        - id : UUID
        - customerId : UUID
        - driverId : UUID
        - paymentReference : String
        - paymentStatus : PaymentStatus
        - status : RideStatus
        
        + assignDriver(driverId) : void
        + accept() : void
        + complete() : void
    }

    class Location <<ValueObject>> {
        + latitude : double
        + longitude : double
    }

    class Money <<ValueObject>> {
        + amount : decimal
        + currency : String
    }

    class MatchingService {
        + matchDriver(pickup, time) : UUID
    }

    class RideStatusPolicy {
        + canAccept(ride, driverId) : boolean
        + canComplete(ride) : boolean
    }

    ' --- NEU: Interne Services & Ports ---
    class PriceCalculationService {
        + calculatePrice(distance, eta) : Money
    }

    interface RouteEtaPort {
        + getRouteAndEta(pickup, destination) : RouteDetails
    }
}

' External Outbound Target Components
component "Persistence Module" as Persistence #LightGray
component "External Maps/Routing Provider" as MapsProvider #LightGreen

' --- Connections ---
' Inbound port routing
Actions ..> RideApplicationService : "execute-command"

' Internal orchestration links
RideApplicationService --> MatchingService
RideApplicationService --> RideStatusPolicy
RideApplicationService --> PriceCalculationService
RideApplicationService --> RouteEtaPort
RideApplicationService ..> Ride : "orchestrates"

' Multiplicity compositions
Ride "1" -- "2" Location : "pickup / destination"
Ride "1" -- "1" Money : "calculated price"

' Outbound boundary paths
RideComponent ..> Persistence : "Save / Load \n(RideRepositoryPort)"
RouteEtaPort ..> MapsProvider : "Abfrage Route/ETA \n(HTTPS / REST)"

@enduml