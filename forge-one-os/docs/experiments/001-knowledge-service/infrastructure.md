# Knowledge Service Infrastructure

## Local-First Runtime

The first runtime target is local Docker Compose.

Required services:

- `frontend`
- `backend-api`
- `backend-worker`
- `postgres`
- `qdrant`
- `minio`
- `ollama`

The backend API and worker can share one image and codebase. They should run as separate processes.

## Proposed Layout

```text
infrastructure/knowledge-service/
├── local/
│   ├── docker-compose.yml
│   └── .env.example
├── docker/
│   ├── backend.Dockerfile
│   ├── frontend.Dockerfile
│   └── worker.Dockerfile
└── README.md
```

## Configuration

No secrets should be committed.

Use `.env.example` for required configuration names:

```text
POSTGRES_HOST=
POSTGRES_PORT=
POSTGRES_DB=
POSTGRES_USER=
POSTGRES_PASSWORD=

QDRANT_URL=

MINIO_ENDPOINT=
MINIO_BUCKET=
MINIO_ACCESS_KEY=
MINIO_SECRET_KEY=

OLLAMA_BASE_URL=
EMBEDDING_MODEL=nomic-embed-text
LLM_MODEL=llama3.1

DEFAULT_ORGANIZATION_ID=
```

## Health Checks

Local Compose should include health checks for:

- PostgreSQL readiness
- Qdrant readiness
- MinIO readiness
- Ollama readiness where practical
- Backend API health endpoint

## Deployment Strategy

Do not design production deployment around a developer laptop.

The initial Docker Compose setup is for local development only. The architecture should allow future deployment to:

- Single VM
- Private customer appliance
- Customer-controlled cloud
- Managed PostgreSQL with private object and vector storage
- Private model runtime

## Data Persistence

Local Compose should use named volumes for:

- PostgreSQL data
- Qdrant collections
- MinIO objects
- Ollama models

## Security Requirements

- Do not expose internal services unnecessarily.
- Frontend should talk to backend API only.
- Backend should be the only service talking to PostgreSQL, Qdrant, MinIO, and Ollama.
- Default credentials in `.env.example` are examples only and must not be production secrets.
- Upload size limits must be enforced by the backend.
- Accepted file types must be validated by content type and file inspection where possible.

## Observability Requirements

The first implementation should include:

- Structured backend logs
- Request IDs
- Indexing job state
- Indexing error summaries
- Model provider and model name on generated answers

Metrics and tracing can come later, but the logging shape should leave room for them.

