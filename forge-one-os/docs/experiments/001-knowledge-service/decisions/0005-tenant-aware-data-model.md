# ADR 0005: Tenant-Aware Data Model

## Status

Accepted

## Context

Authentication and multi-organization administration are not part of v0.1, but Forge One OS is expected to support multiple businesses over time.

Retrofitting tenancy after data exists is risky.

## Decision

Core tables and vector payloads will include `organization_id` from the beginning.

Local development will use a default organization until real authentication and organization management exist.

## Consequences

Queries and retrieval filters must include tenant scope from day one.

The system gains a little early complexity, but avoids a major future migration and reduces the chance of cross-tenant data leakage.

