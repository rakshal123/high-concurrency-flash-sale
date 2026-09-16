# High-Concurrency Flash Sale System

An event-driven, resilient checkout engine designed to handle flash-sale traffic spikes, eliminate race conditions, and guarantee zero overselling.

## Core Engineering Highlights
* **Strict Idempotency:** Sub-millisecond duplicate request rejection using Redis atomic `SETNX` operations to eliminate double-charges during client retry storms.
* **Concurrency Guard:** Atomic database-level stock deduction ensuring inventory integrity under massive concurrent contention.
* **Reliable Event Delivery:** Implementation of the **Transactional Outbox Pattern** to prevent the dual-write problem across PostgreSQL and Apache Kafka.
* **Fault-Tolerant Consumption:** Dead Letter Queue (DLQ) integration for resilient asynchronous order processing and auditing.
