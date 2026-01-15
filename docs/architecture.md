# LifeOps Architecture

This document describes the current technical architecture of LifeOps.  
It is intended to be **easy to update** as the project evolves.

> Guiding rule: **Records are event streams.** Nothing is silently overwritten.

---

## 1. Goals

- Preserve a complete history of life records (append-only)
- Make reading fast and simple (via projections)
- Keep document storage secure (encryption at rest)
- Support self-hosting first, SaaS later without rewriting core architecture
- Keep components modular (storage, auth, search can evolve independently)

---

## 2. Non-Goals (for this architecture)

- Real-time collaboration
- Social features
- Complex workflow automation in v1
- Storing raw file bytes in Postgres

---

## 3. High-Level System Overview

LifeOps consists of:

- **API Server (Go / net/http)**
  - Auth (email + password)
  - Record/event endpoints
  - Attachment upload/download
  - Search endpoints
- **PostgreSQL**
  - Stores users, sessions
  - Stores record identities and append-only events
  - Stores projection tables for fast reads
  - Stores attachment metadata (not file bytes)
- **Blob Store**
  - Stores encrypted file contents
  - Default: local filesystem
  - Later: S3-compatible storage (MinIO/S3)

---

## 4. Core Concepts

### 4.1 Record (identity)
A `Record` is a stable ID representing a real-life item/event (e.g., “Water heater replacement”).

- Records do not store mutable state directly.
- All changes are captured as events.

### 4.2 Record Event (append-only)
A `RecordEvent` is an immutable entry appended to a record’s history.

- Examples: `record_created`, `record_updated`, `note_added`, `attachment_added`, `record_deleted`
- Events include:
  - `occurred_at` (the real-life date/time)
  - `created_at` (when the user recorded it)
  - `payload` (JSON)

### 4.3 Projection (read model)
A projection is a **materialized “current state”** built from events.

- Purpose: fast listing/searching without replaying events every time
- Stored in a `record_view` (or similar) table
- Updated when new events are appended

---

## 5. Data Model Overview (Conceptual)

> The exact schema lives in migrations; this is the conceptual map.

- `users`
- `sessions`
- `records`
- `record_events`
- `record_view` (projection/current state)
- `attachments` (metadata; references blob hash)
- `tags` / `record_tags` (or tags stored as array; TBD)

---

## 6. Write Path (How data is created/changed)

### 6.1 Append-only updates
All record changes happen by **appending an event**:

1. Validate event (type + payload shape)
2. Insert event into `record_events`
3. Apply event to projection in the same transaction
4. Commit

**Invariant:** If an event exists, the projection must reflect it.

### 6.2 Transaction boundary
Event append + projection update should be atomic to avoid inconsistencies.

---

## 7. Read Path (How data is displayed)

### 7.1 Lists / summary views
- Read from `record_view` (fast)
- Support filters (tags, category, date ranges)
- Pagination required

### 7.2 Detail views
- Read `record_view` for current state
- Load attachments metadata
- Load event history (paginated)

---

## 8. Attachments Architecture

### 8.1 Storage location
- File bytes are stored in the **blob store**, not Postgres.

### 8.2 Content addressing
- Blob ID is `sha256(file_bytes)`
- Allows deduplication and integrity checks

### 8.3 Encryption
- Files are encrypted **before** being written to the blob store
- Postgres stores metadata only:
  - blob hash
  - filename
  - mime type
  - size
  - record_id + event_id link

### 8.4 Blob store abstraction
Blob storage is abstracted behind an interface so it can switch between:
- Local filesystem (default)
- S3-compatible storage (later)

---

## 9. Authentication & Sessions

### 9.1 Auth model
- Email (or username) + password
- Passwords stored as Argon2id hashes

### 9.2 Sessions
- Cookie-based sessions (HttpOnly)
- Session records stored in Postgres
- Middleware enforces authentication for protected routes

### 9.3 Multi-user vs single-user
- Architecture supports multiple users in a self-hosted instance
- Each record belongs to a user (ownership enforced in queries)

---

## 10. Search

### 10.1 MVP search
- Postgres Full Text Search (FTS)
- `tsvector` + `GIN` indexes on projection/search fields

### 10.2 Future options
- External search engine (Meilisearch/Typesense) only if needed later

---

## 11. API Design

### 11.1 API style
- REST + JSON
- Versioned routes when needed (e.g., `/api/v1/...`)

### 11.2 Core endpoints (conceptual)
- Auth: register/login/logout/me
- Records: create/list/get
- Events: append/list history
- Attachments: upload/download
- Search: query endpoint

(Exact contracts live in a future `docs/api.md` if needed.)

---

## 12. Configuration

### 12.1 Configuration sources
- Environment variables (primary)
- Optional config file later

### 12.2 Config concerns
- DB connection string
- Blob store path / S3 credentials
- Encryption settings
- Session settings (expiry, cookie flags)

---

## 13. Deployment

### 13.1 Self-host baseline
- Docker Compose:
  - Postgres container
  - lifeops server container
  - volume for blob storage

### 13.2 SaaS later
- Same app
- Multi-tenant controls and ops tooling added without rewriting core data model

---

## 14. Invariants (Must remain true)

- Events are append-only (no updates/deletes)
- Projection reflects events
- Files are not stored in Postgres as BLOBs
- Attachments are encrypted at rest
- Record access is enforced by user ownership

---

## 15. Open Questions (Track here)

Update this section as you make decisions.

- Tag modeling: join table vs array vs JSONB?
- Category model: user-defined vs predefined?
- Encryption key management: per-user key storage strategy?
- Event payload versioning strategy?

---

## 16. Change Log (Architecture updates)

Add entries when you change architecture decisions:

- YYYY-MM-DD — <summary of change>
