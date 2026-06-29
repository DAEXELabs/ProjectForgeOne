# ADR 0001: Local-First Runtime

## Status

Accepted

## Context

Forge One OS is intended to support businesses that may require private compute, local control, and limited dependency on hosted AI services.

The Knowledge Service experiment must prove that document ingestion, retrieval, and question answering can work fully locally.

## Decision

Version 0.1 will run locally through Docker Compose using:

- PostgreSQL for relational data
- Qdrant for vector search
- MinIO for object storage
- Ollama for embeddings and chat inference
- FastAPI for backend API and worker processes
- Next.js for frontend

## Consequences

This increases local infrastructure complexity, but it validates the private-compute posture early.

The service must avoid hard dependencies on a hosted AI provider.

Future production deployments may replace local dependencies with managed or customer-controlled equivalents, but the application should depend on interfaces rather than provider-specific calls in business logic.

