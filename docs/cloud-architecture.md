# Cloud Architecture Overview

```mermaid
graph TD
    %% Define Nodes and Styles
    classDef external fill:#f9f,stroke:#333,stroke-width:2px;
    classDef security fill:#ff9,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5;
    classDef compute fill:#dfd,stroke:#333,stroke-width:1px;
    classDef data fill:#ddf,stroke:#333,stroke-width:2px;
    classDef ops fill:#eee,stroke:#333,stroke-width:1px;

    Client((External Traffic<br>Browser/Mobile)):::external -->|Internet| Edge ingress Tunnel:::security

    subedge [Edge Network & Application Boundary]
        %% Ingress & Routing
        Edge-ingress-Tunnel[Firebase Hosting Rewrites<br>Google Cloud API Gateway]:::compute
        Armor[Google Cloud Armor<br>WAF & DDoS Shield]:::security -.->|Protects| Edge ingress Tunnel
        AppCheck[Firebase App Check<br>Device Attestation]:::security -.->|Integrates| Client

        %% Frontend
        FE[React 18 Frontend<br>feature-based modules]:::compute
        Vite[Vite Build Engine]:::compute -.->|Optimizes| FE

        %% Backend Services (Serverless)
        Auth[Firebase Auth<br>Modular SDK + Custom Claims RBAC]:::compute
        Functions[Google Cloud Functions<br>Vector Math & Triggers]:::compute
        Vertex[Google Cloud Vertex AI<br>Text & Multimodal Embeddings]:::compute

        %% Data & Caching
        CDN[Firebase Hosting CDN<br>Static Assets Caching]:::data
        Memory[Google Cloud Memorystore<br>Redis Semantic/Rate-Limit Cache]:::data
        FS[(Firestore NoSQL<br>Flattened Documents & Subcollections)]:::data

        %% Operations & Infrastructure
        GCOps[Google Cloud Operations Suite<br>Logging, Tracing, Monitoring]:::ops
    end

    %% Define Connections & Protocols
    Client <-->|HTTPS:443 / HMR| FE
    FE <-->|Rest State: TanStack/SWR| Edge ingress Tunnel
    FE -.->|Global State: Zustand| FE
    Edge ingress Tunnel -->|gRPC/REST| Auth
    Edge ingress Tunnel -->|Rewrites| Functions
    Edge ingress Tunnel -->|Proxy/Rewrites| FS

    %% Internal Service Interactions
    Auth -.->|Validates JWT| Functions
    Functions <-->|TTL Caching| Memory
    Functions <--> Vertex
    Functions <-->|Read/Write| FS
    FS -.->|Triggers| Functions
    FS -.->|Native KNN Vector Indexing| FS

    %% Telemetry & CD
    GCOps -.->|Unified Telemetry| subedge
    FE -.->|Vitals Logging| GCOps
    GitHubActions[GitHub Actions<br>Trunk-Based CI/CD]:::ops -.->|Deploys| FE
    GitHubActions -.->|Deploys| Functions
    GitHubActions -.->|Local Emulator Testing| GitHubActions
```