# High-Concurrency Flash Sale System

An event-driven, resilient checkout engine designed to handle flash-sale traffic spikes, eliminate race conditions, and guarantee zero overselling.

## Core Engineering Highlights
* **Strict Idempotency:** Sub-millisecond duplicate request rejection using Redis atomic `SETNX` operations to eliminate double-charges during client retry storms.
* **Concurrency Guard:** Atomic database-level stock deduction ensuring inventory integrity under massive concurrent contention.
* **Reliable Event Delivery:** Implementation of the **Transactional Outbox Pattern** to prevent the dual-write problem across PostgreSQL and Apache Kafka.
* **Fault-Tolerant Consumption:** Dead Letter Queue (DLQ) integration for resilient asynchronous order processing and auditing.

## System Requirements

### Functional Requirements
* **Product Catalog:** Expose endpoints for users to fetch product metadata and current flash-sale pricing.
* **Real-Time Inventory Status:** Return immediate availability status (e.g., "In Stock" or "Sold Out") alongside product details.
* **Flash Sale Checkout:** Accept checkout requests containing a user ID, product ID, quantity, and a unique idempotency key.
* **Order Lifecycle:** Track order progression through a strict state machine: `PENDING` $\rightarrow$ `CONFIRMED` or `FAILED`.
* **Asynchronous Fulfillment:** Trigger downstream notification and analytics events upon successful order confirmation.

### Non-Functional Requirements & Engineering Constraints
* **Zero-Overselling Guarantee:** Enforce absolute data consistency; the system must physically prevent the sale of more items than what exists in the inventory table, regardless of concurrent request volume.
* **Strict Idempotency:** Intercept network retries and duplicate user clicks at the cache layer (Redis) to prevent double-charging and shield the database from redundant connection overhead.
* **High Read Throughput:** Serve product details in sub-millisecond latencies using a Cache-Aside pattern (Redis), falling back to PostgreSQL on cache misses.
* **Resilient Event Streaming:** Guarantee at-least-once delivery of order events to downstream consumers without distributed data loss, using the **Transactional Outbox Pattern** and **Apache Kafka**.
* **Fault Tolerance:** Implement a Dead Letter Queue (DLQ) strategy for Kafka consumers to route poisoned messages safely without blocking partition progress.
