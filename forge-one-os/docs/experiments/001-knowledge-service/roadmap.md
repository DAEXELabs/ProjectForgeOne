# Knowledge Service Roadmap

## Phase 1: Architecture Foundation

Deliverables:

- Experiment documentation
- Architecture notes
- API draft
- Database draft
- Infrastructure draft
- Decision records

Exit criteria:

- Scope is clear
- Service boundaries are documented
- Major technical decisions have an owner and rationale

## Phase 2: Local Infrastructure

Deliverables:

- Docker Compose for PostgreSQL, Qdrant, MinIO, Ollama
- `.env.example`
- Named volumes
- Health checks
- Local run instructions

Exit criteria:

- A developer can start infrastructure locally with one command
- All dependencies report healthy status

## Phase 3: Backend Skeleton

Deliverables:

- FastAPI application
- Typed settings
- Health endpoint
- Structured logging
- Database connection
- Alembic migrations
- Test harness

Exit criteria:

- Backend starts locally
- Health endpoint verifies core dependencies
- Initial database schema is migratable

## Phase 4: Document Upload

Deliverables:

- Upload endpoint
- PDF validation
- MinIO storage
- Document records
- Indexing job records

Exit criteria:

- User can upload a PDF
- Original file is stored
- Document state is visible
- Indexing job is queued

## Phase 5: Indexing Worker

Deliverables:

- Worker process
- PDF text extraction
- Chunking
- Embedding generation through Ollama
- Qdrant vector writes
- Chunk persistence in PostgreSQL
- Job status updates

Exit criteria:

- Uploaded PDFs become indexed documents
- Failed indexing jobs produce useful errors
- Retrieval payloads include tenant and citation metadata

## Phase 6: Question Answering

Deliverables:

- Question endpoint
- Retrieval layer
- Prompt construction
- Ollama chat completion
- Stored conversations
- Stored citations

Exit criteria:

- User can ask a question against indexed documents
- Answer includes citations
- Citations map back to document chunks and pages

## Phase 7: Frontend Console

Deliverables:

- Next.js app
- Document upload view
- Document list and status
- Question interface
- Answer and citation display
- Conversation history

Exit criteria:

- A user can complete the full v0.1 workflow from the browser

## Phase 8: Hardening

Deliverables:

- Error states
- Retry behavior
- Better test coverage
- Developer setup docs
- Basic performance review
- Security review

Exit criteria:

- System is suitable for real internal use against real technical PDFs

## Future Versions

Potential future work:

- OCR
- Image extraction
- Word documents
- Excel documents
- CAD drawings
- Multiple organizations
- Permissions
- Hybrid search
- Reranking
- Streaming answers
- Deployment packaging

