# `newton serve` HTTP API

`newton serve` runs the Axum server with dashboard, portfolio, plans, workflows, HIL, operators, and persistence routes. **`newton serve --help` currently documents only part of this surface.** Use this reference and the OpenAPI file as accurate contracts.

## OpenAPI spec (REST)

**Bundled copy (works offline):** [newton-backend-parity.yaml](newton-backend-parity.yaml) — same file as `openapi/newton-backend-parity.yaml` in the Newton repository; refresh that copy when parity paths or schemas change.

`info.version` tracks the generated spec. Schemas and query parameters live in the YAML file.

## REST endpoints (parity spec)

Placeholder syntax matches OpenAPI: `{id}`, `{event_id}`, `{node_id}`, `{key}`.

### Health

| Method | Path |
| --- | --- |
| GET | `/health` |

### Dashboard

| Method | Path |
| --- | --- |
| GET | `/api/products` |
| GET | `/api/components` |
| GET | `/api/pending-approvals` |
| GET | `/api/regressions` |
| GET | `/api/indicators` |
| GET | `/api/recent-actions` |

### Portfolio

| Method | Path |
| --- | --- |
| GET | `/api/repos` |
| GET | `/api/repo-dependencies` |
| GET | `/api/module-dependencies` |
| POST | `/api/module-dependencies` |
| GET | `/api/saved-views` |

### Opportunities and requests

| Method | Path |
| --- | --- |
| GET | `/api/opportunities` |
| PATCH | `/api/opportunities/{id}` |
| GET | `/api/requests` |
| POST | `/api/requests` |

### Plans and executions

| Method | Path |
| --- | --- |
| GET | `/api/plans` |
| GET | `/api/plans/{id}` |
| POST | `/api/plans/{id}/approve` |
| POST | `/api/plans/{id}/reject` |
| GET | `/api/executions` |

### Workflows

| Method | Path |
| --- | --- |
| GET | `/api/workflows` |
| POST | `/api/workflows` |
| GET | `/api/workflows/{id}` |
| PUT | `/api/workflows/{id}` |
| PATCH | `/api/workflows/{id}/nodes/{node_id}` |

### HIL

| Method | Path |
| --- | --- |
| GET | `/api/hil/instances` |
| GET | `/api/hil/workflows/{id}` |
| POST | `/api/hil/workflows/{id}/{event_id}/action` |

### Operators

| Method | Path |
| --- | --- |
| GET | `/api/operators` |

### Persistence

| Method | Path |
| --- | --- |
| GET | `/api/persistence/{key}` |
| PUT | `/api/persistence/{key}` |
| DELETE | `/api/persistence/{key}` |

### Testing (forbidden when `NEWTON_ENV=production`)

| Method | Path |
| --- | --- |
| POST | `/api/testing/reset` |

## Streaming and legacy

These are implemented in Newton but **not** fully represented in `newton-backend-parity.yaml` today. Inspect `crates/core/src/api/streaming.rs` for exact paths and behavior.

| Kind | Path |
| --- | --- |
| WebSocket | `/api/stream/workflow/{id}/ws` |
| SSE | `/api/stream/workflow/{id}/sse` |
| WebSocket | `/api/stream/logs/{id}/{node_id}/ws` |

Legacy compatibility: `GET /api/channels`.

## Backend storage

`newton serve` initializes SQLite under `.newton/state/backend.sqlite` by default (`newton` CLI `commands.rs`).
