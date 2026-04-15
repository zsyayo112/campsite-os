# System Overview

## High-level architecture

```mermaid
flowchart LR
  subgraph Client
    Browser[Browser / WeChat WebView]
  end

  subgraph Edge
    Nginx[Nginx Reverse Proxy]
  end

  subgraph Apps
    Web[Next.js 14<br/>App Router]
    API[NestJS API]
  end

  subgraph Data
    PG[(PostgreSQL<br/>+ pgvector)]
    Redis[(Redis)]
    Queue[BullMQ Workers]
  end

  subgraph AI
    LLM[OpenAI / Claude API]
  end

  subgraph Obs
    Sentry[Sentry]
    OTel[OTel Collector]
    Prom[Prometheus]
    Graf[Grafana]
  end

  Browser --> Nginx
  Nginx --> Web
  Nginx --> API
  Web --> API
  API --> PG
  API --> Redis
  API --> Queue
  API --> LLM
  Queue --> PG
  Queue --> LLM
  API -.-> Sentry
  Web -.-> Sentry
  API -.-> OTel
  OTel --> Prom
  Prom --> Graf
```

## Bounded contexts

- **identity**: User (staff), Customer (guest)
- **campsite**: Package, PackageItem, PickupLocation
- **booking**: Booking, Order, OrderItem, Payment
- **ai**: Conversation, Message, KnowledgeDoc, KnowledgeChunk

## Request lifecycle (booking creation)

```mermaid
sequenceDiagram
  participant U as Guest
  participant W as Next.js
  participant A as NestJS API
  participant D as PostgreSQL
  participant Q as BullMQ
  U->>W: Submit booking form
  W->>A: POST /bookings (Zod-validated)
  A->>A: BookingService.create
  A->>D: INSERT booking
  A->>Q: enqueue notify-staff job
  A-->>W: 201 Created + booking code
  W-->>U: Show confirmation page
  Q->>A: process notification
```