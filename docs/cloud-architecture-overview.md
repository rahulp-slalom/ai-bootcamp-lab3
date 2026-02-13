# Cloud Architecture Overview

A simple system context for the TODO monorepo:
- React frontend (SPA) in `packages/frontend`
- Express API in `packages/backend`
- In-memory data store using SQLite (`better-sqlite3`) with `:memory:` DB

```mermaid
flowchart TD
  user[User]
  frontend[React Frontend SPA]
  api[Express API Node.js]
  db[(In-memory Store<br/>SQLite :memory:)]

  user --> frontend
  frontend -->|HTTP JSON /api/*| api
  api -->|SQL queries| db

  classDef boundary fill:#f3f7ff,stroke:#3949ab,stroke-width:1px,color:#1a237e
  classDef service fill:#e3f2fd,stroke:#1976d2,stroke-width:1px,color:#0d47a1
  classDef datastore fill:#fff3e0,stroke:#fb8c00,stroke-width:1px,color:#e65100

  class user boundary
  class frontend service
  class api service
  class db datastore
```

- Frontend dev server: `http://localhost:3000` (proxy to backend via `proxy` in `packages/frontend/package.json`)
- Backend API server: `http://localhost:3030`
- Primary endpoints: `/api/tasks` (GET, POST), `/api/tasks/:id` (GET, PUT, PATCH, DELETE)

## Sequence: Create a TODO

```mermaid
sequenceDiagram
  participant U as User
  participant F as React Frontend
  participant A as Express API
  participant D as SQLite In-memory

  U->>F: Enter title, description, due date, priority
  F->>A: POST /api/tasks with task payload
  A->>A: Validate title, normalize due_date, default priority P3
  A->>D: INSERT task record
  D-->>A: Return new task row
  A-->>F: 201 Created JSON response
  F->>F: Re-fetch task list
  F-->>U: Show new task in TaskList
```
