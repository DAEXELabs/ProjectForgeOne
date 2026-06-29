# Knowledge Service Architecture

## Architectural Intent

Knowledge Service is a local-first retrieval-augmented generation service for technical documents.

The service should be built as one bounded domain with clean internal boundaries. It should not become a general assistant, identity system, file drive, or platform shell.

## Service Boundary

Knowledge Service owns:

- Document ingestion
- Document metadata
- PDF text extraction
- Chunking and indexing
- Embedding generation
- Retrieval
- Cited answer generation
- Conversation history over document knowledge

Knowledge Service does not own:

- User identity
- Organization lifecycle management
- Billing
- Global permissions
- General-purpose object storage
- Non-document assistant memory

## Top-Level Repo Layout

```text
forge-one-os/
├── docs/
│   └── experiments/
│       └── 001-knowledge-service/
├── frontend/
│   └── knowledge-service/
├── backend/
│   └── knowledge-service/
├── infrastructure/
│   └── knowledge-service/
├── design/
│   └── knowledge-service/
└── notes/
    └── experiment-001.md
```

## Backend Layout

```text
backend/knowledge-service/
├── app/
│   ├── main.py
│   ├── api/
│   │   └── v1/
│   ├── application/
│   ├── domain/
│   ├── infrastructure/
│   ├── core/
│   └── tests/
├── pyproject.toml
├── Dockerfile
└── README.md
```

### Backend Boundaries

API routes translate HTTP requests into application use-case calls.

Application services coordinate workflows such as document upload, indexing, retrieval, and question answering.

Domain modules define service concepts such as documents, chunks, conversations, citations, and indexing jobs.

Infrastructure adapters integrate with PostgreSQL, Qdrant, MinIO, Ollama, and PDF extraction libraries.

Use cases should depend on interfaces, not directly on storage or model clients.

## Worker Layout

The API and worker may live in the same backend codebase and Docker image, but they should run as separate processes.

The worker owns:

- Pulling queued indexing jobs
- Extracting text from PDFs
- Creating chunks
- Generating embeddings
- Writing vectors to Qdrant
- Updating document and job status

Indexing should not run inside a blocking upload request.

## Frontend Layout

```text
frontend/knowledge-service/
├── app/
│   ├── page.tsx
│   ├── documents/
│   ├── conversations/
│   └── settings/
├── components/
│   ├── documents/
│   ├── chat/
│   ├── citations/
│   └── layout/
├── lib/
│   ├── api/
│   ├── types/
│   └── utils/
├── Dockerfile
├── package.json
└── README.md
```

The first screen should be the working Knowledge Service console: upload documents, view indexing status, ask questions, and inspect citations.

## Request Flow

### Upload Flow

1. User uploads a PDF.
2. API stores the file in MinIO.
3. API creates a `documents` row.
4. API creates an `indexing_jobs` row.
5. Worker processes the job asynchronously.
6. Worker stores chunks in PostgreSQL and vectors in Qdrant.
7. Document state becomes `indexed` or `failed`.

### Question Flow

1. User asks a question.
2. API creates or updates a conversation.
3. API embeds the query.
4. Retrieval layer searches Qdrant with tenant and document filters.
5. API builds a constrained prompt from retrieved chunks.
6. Chat model generates an answer.
7. API stores answer, citations, and model metadata.
8. API returns answer and citations to the frontend.

## Multi-Tenancy Posture

Authentication is deferred, but tenancy is not.

Core tables should include `organization_id` from the beginning. Local development can use a default local organization. This avoids painful migrations later and keeps retrieval filters honest.

## Security Posture

- No hardcoded secrets
- Environment variable configuration
- Typed settings validation at startup
- Tenant-aware queries
- File type validation for uploads
- Size limits for uploads
- No LLM-generated citation IDs
- No direct frontend access to MinIO, Qdrant, or Ollama in v0.1

## Maintainability Rules

- Do not put business workflows in route handlers.
- Do not let use cases call Qdrant, MinIO, Ollama, or PostgreSQL clients directly.
- Do not block upload requests on indexing.
- Do not store citations as unstructured answer text only.
- Do not build platform-wide features until this service proves the need.

