# Project 3 — Personal Helper

> **Go Learning Project** | Next.js · React Native · Go · PostgreSQL · Redis · Docker · Kubernetes · Prometheus · Logging · D3.js

---

## Overview

A full-stack personal productivity system with a Go REST API backend, Next.js frontend, and production-grade infrastructure. Features: Habit Tracker, Journaling, Goals/Milestones, Mood Tracker, Notes, and Reminders.

**Reference languages:** Node.js + TypeScript
**Prior projects:** Project 1 (OAuth2, HTTP client) · Project 2 (database/sql, interfaces, goroutines, CLI)
**Go comfort level entering this project:** Functional with concurrency. Server context will make it natural.
**Auth:** JWT from scratch in Go + Redis session store
**Learning mode:** Deep on all layers — Go API, Infrastructure, Next.js

---

## What This Project Teaches

| Phase                | Technology                        | Go / System Concept                                    | Primary Gotcha                                                 |
| -------------------- | --------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------- |
| 1 — Skeleton         | Go, Docker Compose                | Multi-service architecture, 12-Factor App              | Never hardcode service URLs — use env vars                     |
| 2 — Schema           | PostgreSQL                        | Relational design, migrations as code                  | Never use `VARCHAR(255)` as a default — use `TEXT` in Postgres |
| 3 — HTTP Server      | Go `net/http` + `chi`             | Request lifecycle, middleware chain                    | `http.Handler` is an interface, not a function                 |
| 4 — JWT + Redis      | Go, Redis                         | Stateless vs stateful auth, token invalidation         | JWTs cannot be revoked — Redis session store solves this       |
| 5 — Core API         | Go                                | Repository pattern, handler → service → repository     | Mixing business logic into handlers kills testability          |
| 6 — Reminders        | Go goroutines                     | Ticker, scheduled jobs, graceful shutdown              | Goroutine leaks on shutdown without context cancellation       |
| 7 — Redis Cache      | Redis                             | Cache-aside pattern, TTL strategy                      | Stale cache after write — always invalidate on mutation        |
| 8 — Observability    | Prometheus, Zap                   | RED metrics, structured logging                        | Logging errors without context is useless in production        |
| 9 — Kubernetes       | K8s, Docker                       | Pod, Deployment, Service, ConfigMap, Secret            | Never put secrets in ConfigMaps — use Secrets                  |
| 10 — Next.js + D3.js | Next.js, React, D3.js             | App Router, Server Components, D3 integration patterns | D3 and React both own the DOM — use ref or math-only pattern   |
| 11 — React Native    | React Native, NativeWind, Tamagui | Shared logic layer, platform primitives, Expo Router   | `className` and HTML elements don't exist in React Native      |

---

## System Architecture

```
┌─────────────────────────────────────────────────────┐
│                    Kubernetes Cluster                 │
│                                                       │
│  ┌──────────────┐    ┌──────────────────────────┐    │
│  │  Next.js     │───▶│      Go API Server        │    │
│  │  Frontend    │    │  (chi router + middleware) │    │
│  │  :3000       │    │  :8080                    │    │
│  └──────────────┘    └──────────┬───────────────┘    │
│                                 │                     │
│              ┌──────────────────┼──────────────┐      │
│              ▼                  ▼              ▼      │
│      ┌──────────────┐  ┌──────────────┐  ┌────────┐  │
│      │  PostgreSQL  │  │    Redis     │  │Prometheus│ │
│      │  :5432       │  │    :6379     │  │ :9090  │  │
│      └──────────────┘  └──────────────┘  └────────┘  │
└─────────────────────────────────────────────────────┘
```

---

## Phase Plan

| Phase | Description                                            | Status |
| ----- | ------------------------------------------------------ | ------ |
| 1     | Project skeleton + Docker Compose + health endpoint    | 🔜     |
| 2     | PostgreSQL schema + migrations as code                 | 🔜     |
| 3     | Go HTTP server — chi router + three-layer architecture | 🔜     |
| 4     | JWT authentication + Redis session store               | 🔜     |
| 5     | Core API — habits, journal, goals, mood, notes         | 🔜     |
| 6     | Reminders — goroutine scheduler + graceful shutdown    | 🔜     |
| 7     | Redis cache layer — cache-aside pattern                | 🔜     |
| 8     | Observability — Prometheus metrics + Zap logging       | 🔜     |
| 9     | Kubernetes deployment                                  | 🔜     |
| 10    | Next.js frontend + D3.js visualisations                | 🔜     |
| 11    | React Native mobile app                                | 🔜     |

---

## Folder Structure

```
/personal-helper
  /shared                             # shared TypeScript logic
    /api
    /types
    /utils
  /frontend                           # Next.js
  /mobile                             # React Native (Expo)
  /backend                            # Go API
    /cmd/server/main.go
    /internal
      /auth
      /handler
      /service
      /repository
      /cache
      /models
      /metrics
      /logger
      /reminder
    /migrations
    Dockerfile
  /k8s
  docker-compose.yml
  Makefile
```

---

## Key Design Decisions

**Three-layer architecture:** Handler → Service → Repository. Each layer is independently testable. Business logic never lives in handlers.

**JWT + Redis hybrid auth:** JWT handles stateless signature verification. Redis handles revocation (logout, password change, compromised account).

**UUID primary keys:** Generated by the application before insert. Non-sequential, safe for distributed systems.

**Cache-aside pattern:** Check cache → miss → read DB → populate cache. Invalidate on every mutation.

**Server Components first:** Next.js App Router defaults to Server Components. Opt into Client Components only when interactivity is needed.

**D3 math-only pattern:** D3 computes coordinates and scales, React renders SVG elements. Ref pattern only for axes and interactive charts.

---

## Dependencies

### Go Backend

```bash
go get github.com/go-chi/chi/v5
go get github.com/jackc/pgx/v5
go get github.com/redis/go-redis/v9
go get github.com/golang-jwt/jwt/v5
go get golang.org/x/crypto
go get github.com/golang-migrate/migrate/v4
go get github.com/prometheus/client_golang
go get go.uber.org/zap
```

### Next.js Frontend

```bash
npm install next react react-dom typescript
npm install d3 @types/d3
npx shadcn@latest init
```

### React Native

```bash
npx create-expo-app mobile --template tabs
npx expo install expo-router nativewind victory-native
```

---

## Environment Variables

```bash
APP_ENV=development
PORT=8080
DB_URL=postgres://user:pass@localhost:5432/personalhelper
REDIS_URL=redis://localhost:6379
REDIS_SESSION_TTL=86400
JWT_SECRET=change-this-to-a-random-256-bit-string
JWT_EXPIRY=900
LOG_LEVEL=debug
```

---

_Project 3 of 3 — Personal Helper | Full-Stack Production Arc_
