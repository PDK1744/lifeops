## LifeOps — Project Description (v1)

LifeOps is an open-source, self-hosted personal life record system designed to help individuals store, organize, and retrieve important life information in a structured, searchable, and trustworthy way.

Instead of acting as a traditional document drive or note-taking app, LifeOps treats each piece of information as a **life record** backed by an append-only history. Records represent real-world events or situations such as home repairs, medical visits, subscriptions, identity documents, warranties, travel documents, or family milestones. Each record maintains a full history of changes, notes, and attached documents, ensuring nothing is silently overwritten or lost.

LifeOps prioritizes:

- Data ownership and self-hosting
- Append-only historical integrity
- Strong search and retrieval
- Encrypted document storage
- Structured records over folders
- Trustworthy long-term storage

Documents are stored as encrypted blobs on disk or object storage, while PostgreSQL stores only metadata and history. All updates are recorded as immutable events, and the current state is maintained through projections for fast access.

LifeOps is intended to function as a **personal life operations system**, allowing users to answer questions like:

- “Where is that warranty?”
- “When did we replace this?”
- “Who handled this?”
- “What was decided and why?”

The project is developed open-source first, with a self-hosted deployment model. A hosted SaaS option may be offered later without changing the core architecture.

LifeOps is not intended to replace cloud drives, note apps, or journaling tools. It exists to provide structure, continuity, and trust for important personal life information.