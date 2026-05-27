%% Kritik (teilweise eingearbeitet): Zu überflächig (muss tief gehen), soll Architektur besser abbilden

classDiagram
    %% ==========================================
    %% DOMAIN LAYER (Purer Geschäfts-Code, keine externen APIs)
    %% ==========================================
    
    class RideStatus {
        <<enumeration>>
        REQUESTED, DRIVER_ASSIGNED, IN_PROGRESS, COMPLETED
    }

    class Location {
        <<Value Object>>
        +double latitude
        +double longitude
    }

    class Money {
        <<Value Object>>
        +double amount
        +String currency
    }

    class Ride {
        <<Aggregate Root>>
        -UUID id
        -UUID passengerId
        -UUID driverId
        -Location pickup
        -Location dropoff
        -RideStatus status
        +request(UUID passengerId, Location pickup, Location dropoff) Ride$
        +assignDriver(UUID driverId) void
        +complete() void
    }

    class RideCompletedEvent {
        <<Domain Event>>
        +UUID rideId
        +UUID passengerId
        +DateTime timestamp
        %% Hinweis: Das Payment-Modul hört dieses Event ab!
    }

    %% ==========================================
    %% APPLICATION LAYER (Use Cases des Moduls)
    %% ==========================================

    class RideApplicationService {
        <<Application Service>>
        -IRideRepository repository
        -IRoutingProvider routingProvider
        -IEventPublisher eventPublisher
        +requestRide(UUID passengerId, Location start, Location end) UUID
        +finishRide(UUID rideId) void
    }

    %% ==========================================
    %% PORTS (Interfaces für Abhängigkeiten nach außen)
    %% ==========================================
    %% Diese Interfaces gehören dem Ride-Modul. 
    %% Wer sie wie implementiert, ist dem Modul egal!

    class IRideRepository {
        <<interface / Port>>
        +save(Ride ride) void
        +findById(UUID rideId) Ride
    }
    
    class IRoutingProvider {
        <<interface / Port>>
        +calculateDistance(Location start, Location end) double
    }
    
    class IEventPublisher {
        <<interface / Port>>
        +publish(DomainEvent event) void
    }

    %% ==========================================
    %% RELATIONS
    %% ==========================================
    
    Ride "1" *-- "1" RideStatus : has
    Ride "1" *-- "2" Location : uses
    
    Ride ..> RideCompletedEvent : generates
    
    RideApplicationService --> IRideRepository : uses
    RideApplicationService --> IRoutingProvider : uses
    RideApplicationService --> IEventPublisher : uses
    RideApplicationService ..> Ride : orchestrates