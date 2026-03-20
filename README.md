# personal-helper

A full-stack personal productivity system — built in Go, Next.js, and React Native.

---

## What It Does

`personal-helper` is a production-grade personal productivity system featuring:

- **Habit Tracker** — daily/weekly habits with streak tracking and heatmap visualisation
- **Journal** — mood-tagged entries with D3 mood trend charts
- **Goals & Milestones** — progress tracking with radial progress rings
- **Mood Tracker** — daily mood logging with trend analysis
- **Notes** — pinnable, taggable notes
- **Reminders** — scheduled notifications via background goroutine scheduler

---

## System Architecture

```
┌────────────────────────────────────────────────────────┐
│                    Kubernetes Cluster                  │
│                                                        │
│  ┌──────────────┐    ┌────────────────────────────┐    │
│  │  Next.js     │───▶│      Go API Server         │    │
│  │  Frontend    │    │  (chi router + middleware) │    │
│  │  :3000       │    │  :8080                     │    │
│  └──────────────┘    └──────────┬─────────────────┘    │
│                                 │                      │
│              ┌──────────────────┼──────────────┐       │
│              ▼                  ▼              ▼       │
│      ┌──────────────┐  ┌──────────────┐  ┌──────────┐  │
│      │  PostgreSQL  │  │    Redis     │  │Prometheus│  │
│      │  :5432       │  │    :6379     │  │ :9090    │  │
│      └──────────────┘  └──────────────┘  └──────────┘  │
└────────────────────────────────────────────────────────┘
```

---

## Tech Stack

### Backend

- **Go 1.26** — API server
- **chi** — HTTP router
- **pgx** — PostgreSQL driver
- **go-redis** — Redis client
- **golang-jwt** — JWT authentication
- **golang-migrate** — database migrations
- **Prometheus** — metrics
- **Zap** — structured logging

### Frontend

- **Next.js 14** — App Router, Server Components
- **D3.js** — data visualisations
- **shadcn/ui** — component library
- **Tailwind CSS** — styling

### Mobile

- **React Native** — Expo Router
- **NativeWind** — Tailwind for React Native
- **Tamagui** — universal components
- **Victory Native** — charts

### Infrastructure

- **Docker** — containerisation
- **Docker Compose** — local development
- **Kubernetes** — production deployment
- **Prometheus** — metrics collection

---

## Project Structure

```
personal-helper/
├── backend/              ← Go API server
│   ├── cmd/server/
│   ├── internal/
│   │   ├── auth/         ← JWT + Redis sessions
│   │   ├── handler/      ← HTTP handlers (thin layer)
│   │   ├── service/      ← business logic
│   │   ├── repository/   ← database access
│   │   ├── cache/        ← Redis cache layer
│   │   ├── models/       ← shared domain types
│   │   ├── metrics/      ← Prometheus registration
│   │   ├── logger/       ← Zap setup
│   │   └── reminder/     ← scheduled job engine
│   └── migrations/       ← SQL migration files
├── frontend/             ← Next.js web app
├── mobile/               ← React Native (Expo)
├── shared/               ← shared TypeScript logic
│   ├── api/              ← typed API client
│   ├── types/            ← shared interfaces
│   └── utils/            ← shared utilities
├── k8s/                  ← Kubernetes manifests
├── docs/                 ← documentation
├── docker-compose.yml
└── Makefile
```

---

## Setup

### Prerequisites

- Go 1.26+
- Node.js 20+
- Docker Desktop
- `make`

### Start All Services

```bash
make up
# → starts PostgreSQL, Redis, Prometheus, Go API, Next.js
```

### Backend Only

```bash
cd backend
go mod tidy
make up   # from root — starts DB + Redis
go run cmd/server/main.go
```

---

## API Reference

| Method | Endpoint                 | Auth | Description          |
| ------ | ------------------------ | ---- | -------------------- |
| `POST` | `/api/v1/auth/register`  | None | Create account       |
| `POST` | `/api/v1/auth/login`     | None | Login, receive JWT   |
| `POST` | `/api/v1/auth/logout`    | JWT  | Invalidate session   |
| `GET`  | `/api/v1/habits`         | JWT  | List habits          |
| `POST` | `/api/v1/habits`         | JWT  | Create habit         |
| `POST` | `/api/v1/habits/:id/log` | JWT  | Log habit completion |
| `GET`  | `/api/v1/journal`        | JWT  | List journal entries |
| `POST` | `/api/v1/journal`        | JWT  | Create journal entry |
| `GET`  | `/api/v1/goals`          | JWT  | List goals           |
| `POST` | `/api/v1/goals`          | JWT  | Create goal          |
| `GET`  | `/api/v1/mood`           | JWT  | List mood logs       |
| `POST` | `/api/v1/mood`           | JWT  | Log mood             |
| `GET`  | `/api/v1/notes`          | JWT  | List notes           |
| `POST` | `/api/v1/notes`          | JWT  | Create note          |
| `GET`  | `/health`                | None | Health check         |
| `GET`  | `/metrics`               | None | Prometheus metrics   |

---

## Phase Progress

| Phase | Description                                         | Status |
| ----- | --------------------------------------------------- | ------ |
| 1     | Project skeleton + Docker Compose + health endpoint | 🔜     |
| 2     | PostgreSQL schema + migrations                      | 🔜     |
| 3     | Go HTTP server — chi + three-layer architecture     | 🔜     |
| 4     | JWT auth + Redis session store                      | 🔜     |
| 5     | Core API — habits, journal, goals, mood, notes      | 🔜     |
| 6     | Reminders — goroutine scheduler + graceful shutdown | 🔜     |
| 7     | Redis cache layer                                   | 🔜     |
| 8     | Observability — Prometheus + Zap                    | 🔜     |
| 9     | Kubernetes deployment                               | 🔜     |
| 10    | Next.js frontend + D3.js visualisations             | 🔜     |
| 11    | React Native mobile app                             | 🔜     |

---

## Related Projects

- [mal-updater](https://github.com/jyotil-raval/mal-updater) — Project 1 · MAL sync CLI + HTTP + gRPC
- [media-shelf](https://github.com/jyotil-raval/media-shelf) — Project 2 · Anime tracking CLI + PostgreSQL

---

## License

[MIT](./LICENSE)
