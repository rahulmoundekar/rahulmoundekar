# Architecture Portfolio

This document captures the engineering patterns demonstrated across the projects featured in this profile.

## 1. Authentication & Tenant Isolation

**Project:** [auth-token-service](https://github.com/rahulmoundekar/auth-token-service)

The service separates authentication concerns from business services and applies multiple security boundaries:

```text
Client
  |
  v
Authentication API
  |
  +--> JWT access token
  +--> Rotating refresh token
  +--> RBAC
  +--> Tenant context
  |
  v
Business services
  |
  v
PostgreSQL
  |
  +--> Row-Level Security
```

Key engineering decisions include storing refresh-token hashes instead of raw tokens, detecting refresh-token reuse, and enforcing tenant isolation at the database layer in addition to application-level authorization.

## 2. Payment Correctness

**Project:** [payment-idempotent-ledger](https://github.com/rahulmoundekar/payment-idempotent-ledger)

The payment flow is designed around correctness under retries and concurrent requests:

```text
Request
  |
  +--> Validate Idempotency-Key
  |
  +--> Lock accounts deterministically
  |
  +--> SERIALIZABLE transaction
  |
  +--> Debit source
  |
  +--> Credit destination
  |
  +--> Create ledger entries
  |
  +--> Validate DEBIT = CREDIT
  |
  +--> Commit
```

The important invariant is that a successful transfer remains balanced in the double-entry ledger while duplicate retries do not create duplicate payments.

## 3. Reliable Event Publishing

**Project:** [event-driven-notification-service](https://github.com/rahulmoundekar/event-driven-notification-service)

The Transactional Outbox pattern keeps database changes and event publication consistent:

```text
                  Transaction
                     |
          +----------+----------+
          |                     |
          v                     v
       Business DB          Outbox Event
                                  |
                                  v
                           Outbox Publisher
                                  |
                                  v
                                Kafka
                              /       \
                             v         v
                       Consumers    Consumers
                           |
                           v
                     Idempotency
                           |
                 +---------+---------+
                 |                   |
              Success              Failure
                                   |
                                   v
                                Retry / DLT
```

Consumer idempotency is modeled using the event identity together with the consumer name, allowing different consumer groups to process the same event independently.

## 4. Reliable Webhook Delivery

**Project:** [webhook-delivery-service](https://github.com/rahulmoundekar/webhook-delivery-service)

The delivery system models the failure path explicitly:

```text
Create event
    |
    v
Persist delivery state
    |
    v
Queue after commit
    |
    v
Worker
    |
    +--> HMAC-SHA256 signing
    |
    +--> Target endpoint
    |
    +--> 2xx -> DELIVERED
    |
    +--> Failure -> RETRYING
                    |
                    +--> Backoff + jitter
                    |
                    +--> Retry limit
                           |
                           v
                     DEAD_LETTER
                           |
                           v
                      Manual retry
```

This makes delivery state observable and gives operators a recovery path when automatic retries are exhausted.

## 5. Search & Data Access

**Project:** [fulltext-search-api](https://github.com/rahulmoundekar/fulltext-search-api)

The search API uses PostgreSQL capabilities before introducing a separate search system:

```text
User query
   |
   v
Full-Text Search
   |
   +--> Match -> rank + paginate
   |
   +--> No match
            |
            v
        pg_trgm
            |
            v
       Fuzzy search
```

The implementation combines `tsvector`, `websearch_to_tsquery()`, `ts_rank()`, trigram similarity and GIN indexes.

## Engineering Principles

### Correctness before convenience

Retries, concurrency, event publication and ledger updates are treated as correctness concerns rather than edge cases.

### Defense in depth

Security is not limited to one filter or controller. Authentication, authorization, tenant context and database-level isolation are layered.

### Explicit failure handling

Retry, dead-letter and recovery states are modeled as part of the architecture instead of being left as implicit operational behavior.

### Observable systems

Health checks, metrics, structured logs and delivery/event state make asynchronous systems easier to operate and debug.

### Prefer the simplest infrastructure that meets the requirement

PostgreSQL is used for transactional search workloads, Redis for asynchronous webhook queueing, and Kafka where durable event-stream semantics are useful.

## Design Topics

- API design and versioning
- Authentication and authorization
- Multi-tenancy
- Idempotency
- Concurrency control
- Transaction isolation
- Double-entry accounting
- Transactional Outbox
- Event versioning
- Consumer idempotency
- Retry and dead-letter handling
- Webhook signing
- PostgreSQL indexing and search
- Observability
- Containerized local environments
