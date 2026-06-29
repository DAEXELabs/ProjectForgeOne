# ADR 0003: PostgreSQL, Qdrant, and MinIO Split

## Status

Accepted

## Context

Knowledge Service needs relational metadata, vector retrieval, and original file storage. No single selected dependency should be forced to do all three jobs.

## Decision

Use:

- PostgreSQL as the system of record
- Qdrant for vector search
- MinIO for original uploaded files

## Consequences

The architecture has more moving parts, but each dependency has a clear responsibility.

The backend must coordinate consistency between document records, stored files, chunks, and vectors.

PostgreSQL remains authoritative. Qdrant and MinIO are operational stores whose contents should be traceable from PostgreSQL records.

