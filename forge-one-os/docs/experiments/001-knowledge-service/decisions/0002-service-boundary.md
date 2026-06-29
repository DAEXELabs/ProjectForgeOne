# ADR 0002: Knowledge Service Boundary

## Status

Accepted

## Context

Forge One OS will eventually contain multiple services. Experiment-001 must avoid becoming a general platform before it solves one real problem.

## Decision

Knowledge Service owns document ingestion, indexing, retrieval, cited answers, and knowledge conversation history.

It does not own identity, billing, organization administration, global permissions, or generic assistant memory.

## Consequences

The service can move quickly without absorbing unrelated platform concerns.

Some placeholder data structures, such as users and organizations, will exist to preserve future boundaries without implementing full authentication in v0.1.

