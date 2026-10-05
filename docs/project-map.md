# Engineering Portfolio

A curated map of Rahul Moundekar's engineering work, organized by the problem each project explores.

## Flagship public projects

| Project | Engineering problem | Key ideas |
|---|---|---|
| [Payment Idempotent Ledger](https://github.com/rahulmoundekar/payment-idempotent-ledger) | Payment correctness under retries and concurrency | Idempotency, deterministic locking, SERIALIZABLE transactions, double-entry ledger |
| [Auth Token Service](https://github.com/rahulmoundekar/auth-token-service) | Secure multi-tenant authentication | JWT, rotating refresh tokens, reuse detection, RBAC, PostgreSQL RLS |
| [Event-Driven Notification Service](https://github.com/rahulmoundekar/event-driven-notification-service) | Reliable event publication and consumption | Transactional outbox, Kafka, idempotent consumers, retries, DLT |
| [Webhook Delivery Service](https://github.com/rahulmoundekar/webhook-delivery-service) | Reliable asynchronous integrations | HMAC signing, Redis queueing, retry backoff + jitter, dead-letter recovery |
| [Full-Text Search API](https://github.com/rahulmoundekar/fulltext-search-api) | Search relevance and efficient retrieval | PostgreSQL FTS, ranking, GIN indexes, pg_trgm fuzzy fallback |
| [Realtime Notification Service](https://github.com/rahulmoundekar/realtime-notification-service) | Low-latency notification delivery | Realtime delivery, backend APIs, event-oriented workflows |

## Private products

These repositories represent larger product work that is kept private:

- **SSO Service** — reusable authentication boundary for applications and microservices.
- **Institute Management** — full-stack institute administration and operational workflows.

## Architecture lens

The portfolio is intentionally centered on four engineering concerns:

**Correctness** → idempotency · concurrency · transactions · invariants

**Security** → authentication · authorization · tenant isolation · defense in depth

**Resilience** → retries · DLT · recovery · explicit failure states

**Operability** → metrics · health checks · structured logs · diagnostics

## Recommended reading path

Start with **Payment Idempotent Ledger** for transaction correctness, move to **Auth Token Service** for security and multi-tenancy, then **Event-Driven Notification Service** for distributed messaging, and finish with **Webhook Delivery Service** for reliable integrations.

For the deeper architectural reasoning, see [Architecture & Engineering Notes](architecture.md).
