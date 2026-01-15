# LifeOps Architecture Decisions

This document records significant technical and architectural decisions made during the development of LifeOps.

Its purpose is to preserve **reasoning**, not just outcomes.

Every entry should answer:
- What was decided?
- Why it was decided?
- What alternatives were considered?
- What was rejected?

---

## Decision Entry Template

Template to mimic for future entries.
<hr>



# YYYY-MM-DD — Short Decision Title 

### Context
Describe the problem or situation that required a decision.

Example:
I needed to decide how to store uploaded documents in a way that supported encryption, portability, and deduplication.

---

### Decision
Clearly state the chosen approach.

Example:
Documents will be stored as encrypted blobs on the filesystem or S3-compatible storage, referenced by content hash from Postgres.

---

### Rationale
Explain why this decision was chosen.

Example:
This approach improves performance, simplifies backups, enables deduplication, and isolates encryption from database concerns.

---

### Consequences
Describe the impact of this decision.

Example:
- Requires blob storage abstraction
- Enables future S3 support
- Simplifies export/import workflows

---

# Example Decision

## 2026-01-15 — Append-only Event Model

### Context
I needed to choose between mutable records or a history-preserving update model.

---

### Decision
LifeOps will use an append-only event model for all record changes.

---

### Rationale
Append-only history preserves trust, enables recovery, and supports long-term auditability.

---

### Consequences
- Requires projection system
- Simplifies rollback and time-travel
- Improves data trust

---

## Notes

- Keep entries concise but complete.
- Never delete old entries.
- If a decision changes, add a new entry referencing the old one.
- This file is a living record of architectural thinking.
