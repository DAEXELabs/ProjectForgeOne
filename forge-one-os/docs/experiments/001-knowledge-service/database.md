# Knowledge Service Database

## Principles

PostgreSQL is the system of record.

Qdrant stores searchable vectors, not authoritative document metadata.

MinIO stores original uploaded files.

Every durable business object should be traceable through PostgreSQL.

## Core Tables

### organizations

Represents a tenant boundary.

Initial local mode uses one default organization.

Fields:

- `id`
- `name`
- `slug`
- `created_at`
- `updated_at`
- `deleted_at`

### users

Placeholder for future authentication.

Fields:

- `id`
- `organization_id`
- `email`
- `display_name`
- `created_at`
- `updated_at`
- `deleted_at`

### documents

Represents an uploaded source document.

Fields:

- `id`
- `organization_id`
- `uploaded_by_user_id`
- `title`
- `original_filename`
- `content_type`
- `storage_bucket`
- `storage_key`
- `status`
- `page_count`
- `sha256`
- `created_at`
- `updated_at`
- `deleted_at`

Document statuses:

- `uploaded`
- `processing`
- `indexed`
- `failed`
- `deleted`

### indexing_jobs

Tracks asynchronous indexing work.

Fields:

- `id`
- `organization_id`
- `document_id`
- `status`
- `attempt_count`
- `error_code`
- `error_message`
- `started_at`
- `completed_at`
- `created_at`
- `updated_at`

Job statuses:

- `queued`
- `running`
- `succeeded`
- `failed`
- `retrying`
- `cancelled`

### document_chunks

Stores text chunks extracted from documents.

Fields:

- `id`
- `organization_id`
- `document_id`
- `chunk_index`
- `page_start`
- `page_end`
- `text`
- `token_count`
- `embedding_model`
- `qdrant_collection`
- `qdrant_point_id`
- `created_at`
- `updated_at`

### conversations

Represents a question-answer thread.

Fields:

- `id`
- `organization_id`
- `created_by_user_id`
- `title`
- `created_at`
- `updated_at`
- `deleted_at`

### conversation_messages

Stores user and assistant messages.

Fields:

- `id`
- `organization_id`
- `conversation_id`
- `role`
- `content`
- `model_provider`
- `model_name`
- `created_at`

Roles:

- `user`
- `assistant`
- `system`

### citations

Links assistant messages to source chunks.

Fields:

- `id`
- `organization_id`
- `message_id`
- `document_id`
- `chunk_id`
- `page_number`
- `source_text_excerpt`
- `score`
- `created_at`

## Indexing Requirements

Add indexes for:

- `documents.organization_id`
- `documents.status`
- `documents.sha256`
- `indexing_jobs.status`
- `indexing_jobs.document_id`
- `document_chunks.document_id`
- `document_chunks.qdrant_point_id`
- `conversations.organization_id`
- `conversation_messages.conversation_id`
- `citations.message_id`

## Vector Data

Qdrant point payloads should include:

- `organization_id`
- `document_id`
- `chunk_id`
- `page_start`
- `page_end`
- `embedding_model`

Retrieval must filter by `organization_id`.

## Migration Strategy

Use Alembic from the first backend scaffold.

Schema changes should be versioned. Manual database edits are not acceptable for shared development.

## Deletion Strategy

Use soft deletes for documents and conversations in v0.1.

Document deletion should also remove or tombstone related Qdrant points. The exact deletion behavior should be implemented through a service method, not scattered across route handlers.

