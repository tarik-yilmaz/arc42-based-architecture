%% Kritik: Zu viele Pfeile - ggf auf drawio umsteigen für Level 1

graph TD
    %% Akteure
    Passenger([Fahrgast])
    Driver([Fahrer])

    %% Externe APIs
    BillingAPI([External Billing API])
    MapsAPI([External Maps API])
    GPSAPI([External GPS/Location API])
    AuthAPI([External Authentication API])

    %% Problem Space: Subdomains
    subgraph "Problem Space (Strategic Design)"
        
        subgraph "Core Domain"
            RideMgmt{{"Ride Matching & Routing"}}
        end

        subgraph "Supporting Subdomains"
            UserMgmt["User Profile & Verification"]
            Location["Live Location Tracking"]
        end

        subgraph "Generic Subdomains"
            Notification["Notification (Email, Push)"]
            Authentication["Authentication"]
        end
    end

    %% Verbindungen innerhalb des Systems
    Passenger -->|Bucht Fahrt| RideMgmt
    Driver -->|Nimmt Fahrt an| RideMgmt
    Passenger -->|Verwaltet Fahrten & Profil| UserMgmt
    Driver -->|Verwaltet Fahrten & Profil| UserMgmt
    Passenger -->|Meldet sich an| Authentication
    Driver -->|Meldet sich an| Authentication
    
    UserMgmt -->|Stellt verifizierte Fahrer| RideMgmt
    Location -->|Liefert Geo-Daten| RideMgmt
    RideMgmt -->|Sendet Updates| Notification
    
    %% Verbindungen zu externen APIs
    RideMgmt -->|Triggert Zahlung via| BillingAPI
    RideMgmt -->|Berechnet Routen/ETA via| MapsAPI
    Location -->|Map Matching & Geocoding via| GPSAPI
    Authentication -->|Login bei| AuthAPI

    %% Styling
    classDef core fill:#fff3e0,stroke:#e65100,stroke-width:3px,color:#000000;
    classDef support fill:#e3f2fd,stroke:#0d47a1,stroke-width:2px,color:#000000;
    classDef generic fill:#f5f5f5,stroke:#424242,stroke-width:2px,color:#000000;
    classDef extApi fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px,stroke-dasharray: 5 5,color:#000000;
    classDef actor fill:#ffffff,stroke:#000000,stroke-width:2px,color:#000000;
    
    class RideMgmt core;
    class UserMgmt,Location support;
    class Notification,Authentication generic;
    class BillingAPI,MapsAPI,GPSAPI,AuthAPI extApi;
    class Passenger,Driver actor;