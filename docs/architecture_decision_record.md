flowchart LR
    %% CLIENT LAYER
    subgraph ClientLayer["CLIENT LAYER (FLUTTER)"]
        direction TB
        MobileApps["<b>Mobile Apps</b><br/>iOS (HealthKit) & Android"]
        WebDashboard["<b>Web Dashboard</b><br/>Flutter CanvasKit / WASM"]
        Wearables["<b>Wearable Devices</b><br/>BLE Heart Rate / Sensors"]
        SupabaseAuth["<b>Supabase Auth SDK</b><br/>OAuth2 / PKCE / JWT"]
    end

    %% API & REALTIME LAYER
    subgraph APILayer["API & REALTIME LAYER"]
        direction TB
        KongGW["<b>Kong API Gateway</b><br/>Rate Limiting • JWT Verifier<br/>SSL Termination (TLS 1.3)"]
        NestJS["<b>NestJS Core Service</b><br/>• REST Workouts & Nutrition<br/>• WebSocket Telemetry Hub<br/>• Social Feeds & Gamification"]
        FastAPI["<b>Python FastAPI AI</b><br/>• Pose Estimation (MediaPipe)<br/>• Food Image Recognition<br/>• Dynamic Plan Re-ranking"]
    end

    %% DATA, CACHING & EVENT BUS
    subgraph DataLayer["DATA, CACHING & EVENT BUS"]
        direction TB
        Postgres["<b>PostgreSQL 16 Multi-Engine Database</b><br/>• <b>Relational Core:</b> Users, Profiles, Subscriptions, Social Graph<br/>• <b>TimescaleDB:</b> High-throughput 1Hz Biometric Streams<br/>• <b>pgvector:</b> High-dimensional embeddings for workouts"]
        RedisCluster["<b>Redis 7 Cluster</b><br/>• Live Leaderboards<br/>• Token Revocation Store<br/>• Transient Sensor Cache"]
        S3Storage["<b>AWS S3 (Encrypted)</b><br/>• Encrypted Meal Photos<br/>• Workout Video Blobs"]
        EventBus["<b>Asynchronous Event Bus (Kafka / RabbitMQ)</b><br/>• Video Transcoding<br/>• Weekly Summary Reports<br/>• Push Notifications (APNs / FCM)"]
    end

    %% NETWORKING CONNECTIONS
    MobileApps -->|HTTPS| KongGW
    WebDashboard -->|HTTPS| KongGW
    Wearables -->|WSS / BLE| NestJS
    SupabaseAuth -.->|JWT Auth| KongGW

    KongGW --> NestJS
    KongGW --> FastAPI

    NestJS <-->|gRPC / Internal IPC| FastAPI
    NestJS -->|SQL / TimescaleDB| Postgres
    FastAPI -->|pgvector Embeddings| Postgres

    NestJS --> RedisCluster
    NestJS --> S3Storage
    NestJS --> EventBus
    FastAPI --> EventBus

    %% STYLING
    classDef clientStyle fill:#f0f7ff,stroke:#0284c7,stroke-width:2px,color:#0f172a;
    classDef apiStyle fill:#f8fafc,stroke:#475569,stroke-width:2px,color:#0f172a;
    classDef dataStyle fill:#fdf4ff,stroke:#9333ea,stroke-width:2px,color:#0f172a;

    class ClientLayer,MobileApps,WebDashboard,Wearables,SupabaseAuth clientStyle;
    class APILayer,KongGW,NestJS,FastAPI apiStyle;
    class DataLayer,Postgres,RedisCluster,S3Storage,EventBus dataStyle;
