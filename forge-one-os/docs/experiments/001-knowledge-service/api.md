# Knowledge Service API

## Principles

The API should be stable, explicit, and boring.

Routes should expose service capabilities without leaking implementation details from PostgreSQL, Qdrant, MinIO, or Ollama.

All future tenant-aware routes should accept organization context through authenticated request context. Until authentication exists, local development should use a configured default organization.

## Versioning

All initial endpoints live under:

```text
/api/v1
```

## Health

```text
GET /api/v1/health
```

Returns service status and dependency health where practical.

## Documents

```text
POST /api/v1/documents
GET /api/v1/documents
GET /api/v1/documents/{document_id}
DELETE /api/v1/documents/{document_id}
```

### POST /documents

Uploads a PDF, stores the original file, creates a document record, and queues an indexing job.

The request should return before indexing is complete.

Response includes:

- `document_id`
- `indexing_job_id`
- `status`

### GET /documents

Lists documents visible to the current organization.

Filters to support early:

- `status`
- `created_after`
- `created_before`

### DELETE /documents/{document_id}

Soft deletes the document and removes or tombstones related vectors.

Hard deletion can be added later as a maintenance operation.

## Indexing Jobs

```text
GET /api/v1/indexing-jobs/{job_id}
POST /api/v1/documents/{document_id}/index
```

### GET /indexing-jobs/{job_id}

Returns job state, progress where available, and error summary if failed.

### POST /documents/{document_id}/index

Queues or requeues indexing for a document.

This should be idempotent where possible.

## Chunks

```text
GET /api/v1/documents/{document_id}/chunks
```

Returns extracted chunks for debugging and citation inspection.

This endpoint may be restricted later.

## Questions

```text
POST /api/v1/questions
```

Creates a user message, retrieves relevant chunks, generates an answer, stores citations, and returns the answer.

Request shape:

```json
{
  "conversation_id": "optional-existing-conversation-id",
  "question": "How do I reset the service interval?",
  "document_ids": ["optional-document-filter"],
  "top_k": 8
}
```

Response shape:

```json
{
  "conversation_id": "conversation-id",
  "message_id": "assistant-message-id",
  "answer": "Answer text.",
  "citations": [
    {
      "citation_id": "citation-id",
      "document_id": "document-id",
      "chunk_id": "chunk-id",
      "document_title": "Manual.pdf",
      "page_number": 42,
      "excerpt": "Relevant source excerpt.",
      "score": 0.82
    }
  ],
  "model": {
    "provider": "ollama",
    "model": "llama3.1"
  }
}
```

## Conversations

```text
GET /api/v1/conversations
GET /api/v1/conversations/{conversation_id}
GET /api/v1/conversations/{conversation_id}/messages
DELETE /api/v1/conversations/{conversation_id}
```

Conversation history is part of v0.1, but it should stay separate from document indexing.

## Error Handling

Errors should return structured responses:

```json
{
  "error": {
    "code": "document_not_found",
    "message": "Document not found.",
    "request_id": "request-id"
  }
}
```

## Non-Goals for v0.1

- Public API keys
- OAuth
- Fine-grained document permissions
- Webhook delivery
- Streaming responses
- Cross-organization administration

