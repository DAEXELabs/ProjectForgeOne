# ADR 0004: Clean Architecture with FastAPI

## Status

Accepted

## Context

FastAPI makes it easy to put business logic directly inside route handlers. That is acceptable for small demos but becomes expensive as workflows grow.

## Decision

The backend will separate:

- API routes
- Application use cases
- Domain concepts
- Infrastructure adapters
- Core configuration and errors

Routes should call application services. Application services should depend on interfaces for storage, retrieval, embeddings, chat models, and PDF extraction.

## Consequences

Initial development requires a little more structure.

The payoff is better testability, replaceable infrastructure, and reduced coupling between HTTP behavior and business workflows.

