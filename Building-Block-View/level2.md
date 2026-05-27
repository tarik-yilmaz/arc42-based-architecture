graph TD
    %% Externe Systeme & APIs
    App([Mobile App / API Gateway])
    BillingAPI([External Billing API])
    MapsAPI([External Maps API])
    GPSAPI([External GPS API])

    subgraph "RideShare Bounded Contexts (Solution Space)"
        
        %% Contexts
        BC_Ride[["Ride Management Context"]]
        BC_Identity[["Identity & Verification Context"]]
        BC_Location[["Location Context"]]
        BC_PaymentGateway[["Payment Integration Context (ACL)"]]
    end

    %% Flow der Nutzer-Interaktionen
    App -->|Erstellt Fahrtanfrage| BC_Ride
    App -->|Verwaltet Profil| BC_Identity
    App -->|Sendet Live-Standort| BC_Location
    
    %% Interne Kommunikation (Context Mapping)
    BC_Ride -.->|Abfrage Fahrer-Status| BC_Identity
    BC_Ride -.->|Sucht Fahrer im Umkreis| BC_Location
    BC_Ride -.->|Sende 'RideCompleted' Event| BC_PaymentGateway

    %% Verbindungen zu externen APIs
    BC_PaymentGateway -->|Führt Zahlung aus| BillingAPI
    BC_Ride -->|Routenberechnung| MapsAPI
    BC_Location -->|Reverse Geocoding| GPSAPI

    %% Styling
    classDef context fill:#e3f2fd,stroke:#0d47a1,stroke-width:2px,color:#000000;
    classDef internal fill:#ffffff,stroke:#000000,stroke-width:1px,color:#000000;
    classDef extApi fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px,stroke-dasharray: 5 5,color:#000000;
    
    class BC_Ride,BC_Identity,BC_Location,BC_PaymentGateway context;
    class BC_Ride_Service,BC_Ride_Domain internal;
    class BillingAPI,MapsAPI,GPSAPI,App extApi;