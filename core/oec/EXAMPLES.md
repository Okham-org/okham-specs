# Open Event Contract (OEC) — Examples

**Version**: 0.1.0

> **Disclaimer**: This document is non-normative and describes recommended practices for the OKHAM ecosystem.

This document provides real-world examples of OEC-conformant events.

---

## Table of Contents

1. [User Lifecycle Event](#1-user-lifecycle-event)
2. [Order Payment Event](#2-order-payment-event)
3. [Inventory Stock Event](#3-inventory-stock-event)
4. [Error Event with Severity](#4-error-event-with-severity)
5. [Event Chain with Causality](#5-event-chain-with-causality)

---

## 1. User Lifecycle Event

**Scenario**: A new user registers on a platform.

```yaml
event: 0.1.0
id: user.lifecycle.created
name: User Created Event
version: 1.0.0
status: stable

source:
  capability: user-management-service
  instance: user-svc-node-2
  version: 3.2.1

timestamp: 2026-01-16T10:30:00.000Z

classification:
  domain: user.lifecycle
  lifecycle: created
  severity: info
  scope: public

payload:
  type: "https://okham.org/types/user/1.0.0"
  data:
    user_id: usr_abc123xyz
    email: alice@example.com
    username: alice_smith
    created_at: 2026-01-16T10:30:00Z
    email_verified: false
    account_type: free
```

**Key Points**:
- Past tense naming: `created`, not `create`
- Minimal causality (initial event in a flow)
- Public scope (can be consumed by analytics, notifications, etc.)

---

## 2. Order Payment Event

**Scenario**: A payment is successfully processed for an order.

```yaml
event: 0.1.0
id: order.payment.processed
name: Order Payment Processed
version: 1.2.0
status: stable

source:
  capability: payment-service
  instance: payment-node-3
  version: 4.1.2

timestamp: 2026-01-16T11:15:22.456Z

causality:
  correlation_id: order-req-uuid-abc123
  causation_id: cmd-process-payment-xyz789
  trace_id: trace-dist-id-456
  span_id: span-payment-proc

classification:
  domain: order.payment
  lifecycle: completed
  severity: info
  scope: public
  category: financial.transaction

payload:
  type: "https://okham.org/types/payment/1.0.0"
  data:
    order_id: ord_12345
    payment_id: pay_67890
    amount: 99.99
    currency: USD
    payment_method: credit_card
    card_last_four: "4242"
    status: completed
    processed_at: 2026-01-16T11:15:22Z
    gateway: stripe
    transaction_id: txn_stripe_abc123

metadata:
  region: us-west-2
  idempotency_key: pay-idem-key-123
  retry_count: 0
```

**Key Points**:
- Causality tracking links payment to original order request
- Financial domain with semantic category
- Includes metadata for idempotency and regional routing
- Sensitive data (full card number) excluded

---

## 3. Inventory Stock Event

**Scenario**: A product goes out of stock.

```yaml
event: 0.1.0
id: inventory.stock.depleted
name: Stock Depleted Alert
version: 1.0.0
status: stable

source:
  capability: inventory-service
  instance: inventory-node-1

timestamp: 2026-01-16T12:45:10.000Z

causality:
  correlation_id: order-fulfillment-uuid-xyz
  causation_id: event-order-confirmed-abc

classification:
  domain: inventory.stock
  lifecycle: depleted
  severity: warn
  scope: internal

payload:
  type: "https://okham.org/types/stock-level/1.0.0"
  data:
    product_id: prod_xyz789
    sku: SKU-ABC-123
    warehouse_id: warehouse_west_1
    current_quantity: 0
    previous_quantity: 5
    threshold: 10
    depleted_at: 2026-01-16T12:45:10Z
```

**Key Points**:
- **`severity: warn`** indicates actionable alert
- **`scope: internal`** limits visibility to internal systems
- Causation links to order fulfillment event

---

## 4. Error Event with Severity

**Scenario**: A critical service failure occurs.

```yaml
event: 0.1.0
id: system.service.failed
name: Service Failed Event
version: 1.0.0
status: stable

source:
  capability: database-service
  instance: db-primary-node-1
  version: 5.3.2

timestamp: 2026-01-16T14:22:35.789Z

causality:
  correlation_id: health-check-cron-uuid
  trace_id: trace-health-check-123

classification:
  domain: system.service
  lifecycle: failed
  severity: critical
  scope: internal

payload:
  type: "https://okham.org/types/error/1.0.0"
  data:
    error_code: DB_CONNECTION_LOST
    error_message: "Primary database connection lost after 3 retry attempts"
    service_name: database-service
    error_timestamp: 2026-01-16T14:22:35Z
    retry_count: 3
    last_successful_ping: 2026-01-16T14:20:00Z
    health_status: unhealthy

metadata:
  alert_sent: true
  pagerduty_incident_id: PD-INC-12345
```

**Key Points**:
- **`severity: critical`** triggers alerts and escalation
- Error events are still facts (what failed, when, how)
- Metadata tracks incident management integration

---

## 5. Event Chain with Causality

**Scenario**: A sequence of related events in an order flow.

### Event 1: Order Created

```yaml
event: 0.1.0
id: order.lifecycle.created
name: Order Created
version: 1.0.0
status: stable

source:
  capability: order-service
  instance: order-svc-1

timestamp: 2026-01-16T15:00:00.000Z

causality:
  correlation_id: order-flow-uuid-abc123
  causation_id: user-action-add-to-cart-xyz

classification:
  domain: order.lifecycle
  lifecycle: created
  severity: info
  scope: public

payload:
  type: "https://okham.org/types/order/1.0.0"
  data:
    order_id: ord_99999
    user_id: usr_alice_123
    total_amount: 150.00
    currency: USD
    items_count: 3
    created_at: 2026-01-16T15:00:00Z
```

### Event 2: Payment Processed (caused by Order Created)

```yaml
event: 0.1.0
id: order.payment.processed
name: Order Payment Processed
version: 1.2.0
status: stable

source:
  capability: payment-service
  instance: payment-node-2

timestamp: 2026-01-16T15:00:15.000Z

causality:
  correlation_id: order-flow-uuid-abc123
  causation_id: event-order.lifecycle.created-ord_99999
  trace_id: trace-order-flow-123

classification:
  domain: order.payment
  lifecycle: completed
  severity: info
  scope: public

payload:
  type: "https://okham.org/types/payment/1.0.0"
  data:
    order_id: ord_99999
    payment_id: pay_88888
    amount: 150.00
    currency: USD
    status: completed
```

### Event 3: Order Shipped (caused by Payment Processed)

```yaml
event: 0.1.0
id: order.fulfillment.shipped
name: Order Shipped Event
version: 1.0.0
status: stable

source:
  capability: fulfillment-service
  instance: fulfillment-node-1

timestamp: 2026-01-16T15:30:00.000Z

causality:
  correlation_id: order-flow-uuid-abc123
  causation_id: event-order.payment.processed-pay_88888
  trace_id: trace-order-flow-123

classification:
  domain: order.fulfillment
  lifecycle: shipped
  severity: info
  scope: public

payload:
  type: "https://okham.org/types/shipment/1.0.0"
  data:
    order_id: ord_99999
    shipment_id: ship_77777
    carrier: FedEx
    tracking_number: "1Z999AA10123456784"
    shipped_at: 2026-01-16T15:30:00Z
```

**Key Points**:
- All three events share the same `correlation_id` (order-flow-uuid-abc123)
- Each event's `causation_id` references the preceding event
- Enables full traceability of the order lifecycle

---

## 6. Event Versioning Example

**Scenario**: An event schema evolves from v1.0.0 to v2.0.0 (breaking change).

### Version 1.0.0 (Old)

```yaml
event: 0.1.0
id: notification.email.sent
name: Email Sent Event
version: 1.0.0
status: deprecated

source:
  capability: notification-service

timestamp: 2026-01-16T16:00:00Z

payload:
  type: "https://okham.org/types/email-notification/1.0.0"
  data:
    to: alice@example.com
    subject: "Welcome to OKHAM"
    sent_at: 2026-01-16T16:00:00Z
```

### Version 2.0.0 (New - Breaking Change)

```yaml
event: 0.1.0
id: notification.email.sent
name: Email Sent Event
version: 2.0.0
status: stable

source:
  capability: notification-service
  instance: notify-node-2

timestamp: 2026-01-16T16:00:00Z

payload:
  type: "https://okham.org/types/email-notification/2.0.0"
  data:
    recipients:  # BREAKING: Changed from 'to' to 'recipients' array
      - email: alice@example.com
        name: Alice Smith
    subject: "Welcome to OKHAM"
    template_id: welcome-email-v2  # NEW FIELD
    sent_at: 2026-01-16T16:00:00Z
    delivery_status: sent  # NEW FIELD
```

**Key Points**:
- `version` field incremented to 2.0.0 (MAJOR change)
- Old version marked `deprecated`
- Breaking change: `to` field replaced with `recipients` array
- New fields added: `template_id`, `delivery_status`

---

## Anti-Patterns (What NOT to Do)

### ❌ Command Instead of Event

```yaml
id: user.create  # WRONG: Imperative, not past tense
name: Create User Command  # WRONG: This is a command, not an event
```

### ❌ Missing Required Fields

```yaml
event: 0.1.0
id: order.created
# MISSING: version, status, source, timestamp, payload
```

### ❌ Transport Coupling

```yaml
payload:
  data:
    kafka_topic: orders  # WRONG: Should not reference transport
    nats_subject: order.created  # WRONG: Transport-specific
```

### ❌ Execution Logic

```yaml
payload:
  data:
    execute_hook: send_email  # WRONG: Events don't execute hooks
```

---

**End of Examples**
