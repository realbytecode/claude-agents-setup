# Design: [Feature Name]

> Created by: architect agent
> Date: [Date]
> Status: Draft | Ready for Review | Approved

## Overview
[Brief description of the design approach]

## Architecture

```mermaid
flowchart TB
    %% Add your architecture diagram here
    A[Component A] --> B[Component B]
    B --> C[Component C]
```

## Sequence Flow

```mermaid
sequenceDiagram
    %% Add your sequence diagram here
    participant User
    participant API
    participant Service
    participant DB
    
    User->>API: Request
    API->>Service: Process
    Service->>DB: Query
    DB-->>Service: Result
    Service-->>API: Response
    API-->>User: Response
```

## Data Model

```mermaid
erDiagram
    %% Add your ER diagram here
    ENTITY1 {
        string id PK
        string field1
    }
    ENTITY2 {
        string id PK
        string entity1_id FK
    }
    ENTITY1 ||--o{ ENTITY2 : has
```

## Components

### [Component 1]
- **Responsibility**: 
- **Location**: `src/...`
- **Interfaces**: 

### [Component 2]
...

## API Contract (if applicable)

```
POST /api/resource
Request:
{
  "field": "value"
}

Response:
{
  "id": "...",
  "field": "value"
}
```

## Error Handling
- [Error case 1]: [How handled]
- [Error case 2]: [How handled]

## Edge Cases Considered
- [Edge case 1]
- [Edge case 2]

---

**Next Step**: Invoke `reviewer` agent for design review
