# Mermaid Diagram Examples

Reference patterns for the mermaid-diagrams skill. Read when drafting a specific diagram type.

## Before and after: amateur vs professional

### Before (default styling, vague IDs)

```mermaid
graph TD
    s1 --> s2
    s2 --> s3
    s3 --> s4
```

Problems: `graph` syntax, no theme, cryptic IDs, no labels, no grouping.

### After (themed, labelled, grouped)

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {
  'primaryColor': '#4f46e5', 'primaryTextColor': '#fff',
  'primaryBorderColor': '#3730a3', 'lineColor': '#94a3b8',
  'mainBkg': '#f8fafc', 'clusterBkg': '#f1f5f9', 'clusterBorder': '#e2e8f0'
}}}%%
flowchart TD
    subgraph client [Client]
        webApp[Web App]
    end
    subgraph backend [Backend]
        api[API Service]:::primary
        worker[Worker]:::primary
    end
    db[(PostgreSQL)]:::store

    webApp -->|"HTTPS"| api
    api --> worker
    worker --> db

    classDef primary fill:#4f46e5,stroke:#3730a3,color:#fff
    classDef store fill:#10b981,stroke:#059669,color:#fff
```

## Request flow (sequence)

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {
  'primaryColor': '#4f46e5', 'lineColor': '#94a3b8',
  'actorBkg': '#f8fafc', 'signalColor': '#64748b', 'textColor': '#334155'
}}}%%
sequenceDiagram
    autonumber
    actor User
    participant UI as Web UI
    participant API as API
    participant DB as Database

    User->>UI: Submit order
    UI->>API: POST /orders
    API->>DB: Insert order
    DB-->>API: OK
    API-->>UI: 201 Created
    UI-->>User: Confirmation
```

## Data model (ER)

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#94a3b8'}}}%%
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ ORDER_ITEM : contains
    PRODUCT ||--o{ ORDER_ITEM : "listed in"

    USER {
        uuid id PK
        string email
    }
    ORDER {
        uuid id PK
        uuid user_id FK
        string status
    }
```

## State machine

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {
  'primaryColor': '#4f46e5', 'lineColor': '#94a3b8'
}}}%%
stateDiagram-v2
    [*] --> Draft
    Draft --> Submitted : submit
    Submitted --> Approved : approve
    Submitted --> Rejected : reject
    Rejected --> Draft : revise
    Approved --> [*]
```

## C4 container (with mandatory line styling)

```mermaid
C4Container
    title Container diagram — Order service

    Person(customer, "Customer", "Places orders")
    Container(web, "Web App", "React", "Order UI")
    Container(api, "API", "Node.js", "Order API")
    ContainerDb(db, "Database", "PostgreSQL", "Orders")

  Rel(customer, web, "Uses", "HTTPS")
  Rel(web, api, "Calls", "JSON/HTTPS")
  Rel(api, db, "Reads/writes", "SQL")

  UpdateRelStyle(customer, web, $textColor="#475569", $lineColor="#94a3b8")
  UpdateRelStyle(web, api, $textColor="#475569", $lineColor="#94a3b8")
  UpdateRelStyle(api, db, $textColor="#475569", $lineColor="#94a3b8")
  UpdateLayoutConfig($c4ShapeInRow="3", $c4BoundaryInRow="1")
```

## Splitting a complex system

When a system has more than ~15 components, produce a **series**:

1. **Context** — C4Context: users and external systems
2. **Containers** — C4Container: apps, APIs, databases
3. **Flows** — one sequence diagram per non-trivial path

Link them in prose: *"See Order submission sequence for the path from Web App to Payment Gateway."*
