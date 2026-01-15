# LifeOps

> Your life records, structured and searchable.

LifeOps is an open-source, self-hosted personal life record system designed to help individuals store, organize, and retrieve important life information in a structured, searchable, and trustworthy way.

Instead of acting as a traditional document drive or note-taking app, LifeOps treats each piece of information as a **life record** backed by an append-only history. Records represent real-world events or situations such as home repairs, medical visits, subscriptions, identity documents, warranties, travel documents, or family milestones.

Each record maintains a full history of changes, notes, and attached documents, ensuring nothing is silently overwritten or lost.

---

## Core Principles

LifeOps is built around the following principles:

- **Data ownership** — Users own their data and can self-host the system.
- **Append-only history** — All changes are preserved as immutable events.
- **Structured records** — Information is organized as life records, not loose files.
- **Encrypted attachments** — Documents are encrypted before storage.
- **Searchable continuity** — Records can be searched and understood over time.
- **Trust and longevity** — Designed for long-term personal use.

---

## What LifeOps Is

LifeOps is a personal life operations system that helps answer questions like:

- Where is that warranty?
- When did we replace this?
- Who handled this?
- What was decided and why?

It combines structured metadata, full history, and document storage into a single coherent system.

---

## What LifeOps Is Not

LifeOps is **not**:

- A cloud drive replacement  
- A journaling or diary app  
- A note-taking app  
- A productivity tracker  
- A social platform  

LifeOps focuses strictly on structured, trustworthy life record keeping.

---

## Architecture Overview

LifeOps uses an append-only event model:

- Records are identities.
- All updates are stored as immutable events.
- A projection system maintains the current state for fast access.
- Documents are stored as encrypted blobs on disk or object storage.
- PostgreSQL stores only metadata and history.

This design preserves full history while keeping the system efficient and auditable.

---

## Deployment Model

LifeOps is developed open-source first and is designed to be:

- Fully self-hosted
- Container-friendly
- Storage-agnostic
- Database-backed (PostgreSQL)

A hosted SaaS option may be offered in the future without changing the core architecture.

---

## Project Status

LifeOps is currently in early development.  
The focus is on building a clean, secure, and maintainable foundation before expanding features.

---

## Contributing

Contributions are welcome once the core architecture stabilizes.  
Please review the documentation in `/docs` before submitting changes.

---

## License

AGPL-3.0

---

## Philosophy

LifeOps exists to provide structure, continuity, and trust for important personal life information.

It is designed to grow slowly, deliberately, and transparently.
