# Experiment-001: Knowledge Service

## Purpose

Knowledge Service is the first Forge One OS service experiment.

The experiment tests whether a local-first AI system can reduce the time technicians spend searching through PDF manuals, SOPs, and technical documentation.

## Problem

Technicians lose time and context when they need to search across manuals, service documents, SOPs, and other technical references. Traditional file storage preserves documents but does not make the knowledge inside them easy to query, verify, or reuse.

## Goal

Build a production-quality local-first knowledge system where a user can:

- Upload PDFs
- Automatically index document contents
- Ask natural language questions
- Receive answers with citations back to the original document
- Run the full system locally

## Version 0.1 Scope

Version 0.1 includes:

- PDF upload
- Text extraction
- Document chunking
- Embedding generation
- Vector storage
- Natural language question answering
- Cited answers
- Conversation history
- Document management

Version 0.1 excludes:

- Authentication
- Production multi-organization administration
- OCR
- Image extraction
- Word, Excel, and CAD support
- Fine-grained permissions
- Hosted deployment automation

## Architecture Posture

This is not a disposable prototype. It is a bounded service experiment intended to become the Knowledge Service inside Forge One OS if it proves useful.

The implementation should stay small, but the boundaries should be real:

- Frontend: Next.js 15, TypeScript, TailwindCSS, shadcn/ui
- Backend API: FastAPI, Python 3.12
- Worker: Python process for ingestion and indexing jobs
- Database: PostgreSQL
- Vector database: Qdrant
- Object storage: MinIO
- AI runtime: Ollama with `nomic-embed-text` and configurable chat model
- Infrastructure: Docker Compose for local development

## Documentation Map

- [Architecture](./architecture.md)
- [API](./api.md)
- [Database](./database.md)
- [Infrastructure](./infrastructure.md)
- [Roadmap](./roadmap.md)
- [Decision Records](./decisions/)

