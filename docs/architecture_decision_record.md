# FitFlow System Architecture

## Architecture Diagram

```mermaid
flowchart LR
    %% CLIENT LAYER
    subgraph ClientLayer["CLIENT LAYER (FLUTTER)"]
        direction TB
        Mobile["Mobile Apps<br/>iOS (HealthKit) & Android"]
        Web["Web Dashboard<br/>Flutter CanvasKit / WASM"]
        Wearables["Wearable Devices<br/>BLE Heart Rate / Sensors"]
        AuthSDK["Supabase Auth SDK<br/>OAuth2 / PKCE / JWT"]
    end

    %% API & REALTIME LAYER
    subgraph APILayer["API & REALTIME LAYER"]
        direction TB
        Kong["Kong API Gateway<br/>Rate Limiting · JWT Verifier<br/>SSL Termination (TLS 1.3)"]
        Nest["NestJS Core Service<br/>• REST Workouts & Nutrition<br/>• WebSocket Telemetry Hub<br/>• Social Feeds & Gamification"]
        FastAPI["Python FastAPI AI<br/>• Pose Estimation (MediaPipe)<br/>• Food Image Recognition<br/>• Dynamic Plan Re-ranking"]
    end

    %% DATA, CACHING & EVENT BUS
    subgraph DataLayer["DATA, CACHING & EVENT BUS"]
        direction TB
        Postgres["PostgreSQL 16 Multi-Engine Database<br/>• Relational Core: Users, Profiles, Subscriptions, Social Graph<br/>• TimescaleDB: High-throughput 1Hz Biometric Streams<br/>• pgvector: High-dimensional embeddings for workouts"]
        Redis["Redis 7 Cluster<br/>• Live Leaderboards<br/>• Token Revocation Store"]
        S3["AWS S3 (Encrypted)<br/>• Encrypted Meal Photos<br/>• Workout Video Blobs"]
        Bus["Asynchronous Event Bus (RabbitMQ / Kafka)<br/>• Decoupled async worker tasks: Video Transcoding, Weekly Summary Reports, and Push Notifications via APNs / FCM"]
    end

    %% CONNECTIONS & PROTOCOLS
    Mobile -->|HTTPS| Kong
    Web -->|HTTPS| Kong
    Wearables -->|WSS / BLE| Nest
    AuthSDK -.->|JWT Auth| Kong

    Kong --> Nest
    Kong --> FastAPI

    Nest <-->|gRPC| FastAPI
    Nest --> Postgres
    FastAPI --> Postgres

    Nest --> Redis
    Nest --> S3
    Nest --> Bus
