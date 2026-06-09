    classDiagram
        %% Level 3: White Box Ride Management & Matching
        %% Focus: ride lifecycle, automatic matching, pricing, payment handoff.

        class RideStatus {
            <<enumeration>>
            REQUESTED
            ACCEPTED
            IN_PROGRESS
            COMPLETED
            CANCELLED
        }

        class PaymentStatus {
            <<enumeration>>
            PENDING
            RETRYING
            CONFIRMED
            FAILED
        }

        class Location {
            <<Value Object>>
            +double latitude
            +double longitude
        }

        class Money {
            <<Value Object>>
            +decimal amount
            +String currency
        }

        class Ride {
            <<Aggregate Root>>
            -UUID id
            -UUID customerId
            -UUID driverId
            -Location pickup
            -Location destination
            -Money price
            -String paymentReference
            -PaymentStatus paymentStatus
            -RideStatus status
            +assignDriver(UUID driverId) void
            +accept() void
            +start() void
            +cancel() void
            +complete() void
            +recordPaymentResult(String paymentReference, PaymentStatus status) void
        }

        class RideApplicationService {
            <<Application Service>>
            -RideRepositoryPort repository
            -MatchingService matchingService
            -PriceCalculation priceCalculation
            -RideStatusPolicy statusPolicy
            -PaymentHandoff paymentHandoff
            +searchRides(Location pickup, Location destination, DateTime time) RideOption[]
            +requestRide(UUID customerId, Location pickup, Location destination, DateTime time) UUID
            +acceptRide(UUID rideId, UUID driverId) void
            +startRide(UUID rideId) void
            +cancelRide(UUID rideId, UUID actorId) void
            +completeRide(UUID rideId) void
            +getRideHistory(UUID userId) Ride[]
        }

        class MatchingService {
            <<Domain Service>>
            -DriverVerificationPort driverVerification
            +matchDriver(Location pickup, DateTime time) UUID
        }

        class PriceCalculation {
            <<Domain Service>>
            +calculatePrice(Location pickup, Location destination, DateTime time) Money
        }

        class RideStatusPolicy {
            <<Domain Policy>>
            +canAccept(Ride ride, UUID driverId) boolean
            +canStart(Ride ride) boolean
            +canCancel(Ride ride, UUID actorId) boolean
            +canComplete(Ride ride) boolean
        }

        class PaymentHandoff {
            <<Application Service>>
            -PaymentIntegrationPort paymentIntegration
            +queueRidePayment(Ride ride) void
        }

        class RideRepositoryPort {
            <<interface / Port>>
            +save(Ride ride) void
            +findById(UUID rideId) Ride
            +findHistoryByUser(UUID userId) Ride[]
        }

        class DriverVerificationPort {
            <<interface / Port>>
            +findVerifiedAvailableDrivers(Location pickup, DateTime time) UUID[]
        }

        class PaymentIntegrationPort {
            <<interface / Port>>
            +queuePayment(UUID rideId, Money amount) void
        }

        Ride "1" *-- "1" RideStatus : has
        Ride "1" *-- "1" PaymentStatus : has
        Ride "1" *-- "2" Location : uses
        Ride "1" *-- "1" Money : has

        RideApplicationService --> RideRepositoryPort : stores and loads rides
        RideApplicationService --> MatchingService : selects driver
        RideApplicationService --> PriceCalculation : calculates price
        RideApplicationService --> RideStatusPolicy : validates transitions
        RideApplicationService --> PaymentHandoff : triggers payment on completion
        RideApplicationService ..> Ride : orchestrates

        MatchingService --> DriverVerificationPort : asks for verified drivers
        PaymentHandoff --> PaymentIntegrationPort : queues payment request
