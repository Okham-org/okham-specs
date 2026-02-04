# Open Business Contract (OBC) Examples

**Version**: 0.1.0  
**Status**: DRAFT  
**Last Updated**: 2026-01-17

---

## Overview

This document provides non-normative examples demonstrating OBC business flow composition, step types, and integration with OKHAM Core standards.

---

## 1. Order Fulfillment Flow

A complete e-commerce order fulfillment process with validation, payment, and shipping.

```yaml
id: "order-fulfillment"
name: "Order Fulfillment"
description: "Process customer orders from receipt to delivery"
version: "1.0.0"

inputs:
  - name: "orderId"
    type: "okham_core_identifier"
    description: "Order identifier"
    required: true
  
  - name: "customerId"
    type: "okham_core_identifier"
    description: "Customer identifier"
    required: true

outputs:
  - name: "fulfillmentStatus"
    type: "okham_commerce_fulfillment-status"
    description: "Final fulfillment status"
  
  - name: "trackingNumber"
    type: "okham_core_string"
    description: "Shipping tracking number"
    required: false

state:
  type: "okham_commerce_order-state"
  initial:
    status: "pending"
    items: []
    totalAmount: 0

policies:
  - "order-validation-policy"
  - "payment-compliance-policy"

steps:
  - id: "validate-order"
    type: "action"
    description: "Validate order details and inventory availability"
    action:
      operation: "validate-order"
      parameters:
        orderId: "{{inputs.orderId}}"
    transitions:
      - to: "check-customer-credit"
        condition: "outputs.valid == true"
        description: "Order is valid, proceed to credit check"
        priority: 10
      - to: "reject-order"
        condition: "outputs.valid == false"
        description: "Order validation failed"
        priority: 5
    timeout: "PT30S"
    events:
      - "order-validated"

  - id: "check-customer-credit"
    type: "decision"
    description: "Determine if customer credit check is required"
    transitions:
      - to: "verify-customer-credit"
        condition: "state.totalAmount > 1000"
        description: "High-value order requires credit check"
        priority: 10
      - to: "process-payment"
        condition: "state.totalAmount <= 1000"
        description: "Standard order, proceed to payment"
        priority: 5

  - id: "verify-customer-credit"
    type: "subflow"
    description: "Verify customer credit for high-value orders"
    subflow:
      flowId: "customer-credit-verification"
      version: "2.0.0"
      inputMapping:
        customerId: "{{inputs.customerId}}"
        requestedAmount: "{{state.totalAmount}}"
      outputMapping:
        creditApproved: "{{outputs.approved}}"
        creditLimit: "{{outputs.limit}}"
    transitions:
      - to: "process-payment"
        condition: "outputs.creditApproved == true"
        priority: 10
      - to: "reject-order"
        condition: "outputs.creditApproved == false"
        priority: 5
    timeout: "PT2M"

  - id: "process-payment"
    type: "action"
    description: "Process customer payment"
    action:
      operation: "process-payment"
      parameters:
        customerId: "{{inputs.customerId}}"
        amount: "{{state.totalAmount}}"
        orderId: "{{inputs.orderId}}"
    transitions:
      - to: "allocate-inventory"
        condition: "outputs.paymentStatus == 'success'"
        priority: 10
      - to: "payment-failed"
        condition: "outputs.paymentStatus == 'failed'"
        priority: 5
    retry:
      maxAttempts: 3
      backoff: "exponential"
      delay: "PT5S"
    events:
      - "payment-processed"

  - id: "allocate-inventory"
    type: "action"
    description: "Allocate inventory for order items"
    transitions:
      - to: "ship-order"
        condition: "outputs.allocated == true"
        priority: 10
      - to: "refund-payment"
        condition: "outputs.allocated == false"
        priority: 5
    timeout: "PT1M"

  - id: "ship-order"
    type: "action"
    description: "Ship order to customer"
    action:
      operation: "create-shipment"
      parameters:
        orderId: "{{inputs.orderId}}"
        customerId: "{{inputs.customerId}}"
    transitions:
      - to: "complete"
        condition: "true"
    events:
      - "order-shipped"

  - id: "reject-order"
    type: "action"
    description: "Reject invalid or unapproved order"
    action:
      operation: "reject-order"
      parameters:
        orderId: "{{inputs.orderId}}"
        reason: "{{outputs.rejectionReason}}"
    transitions:
      - to: "complete"
        condition: "true"
    events:
      - "order-rejected"

  - id: "payment-failed"
    type: "action"
    description: "Handle payment failure"
    transitions:
      - to: "complete"
        condition: "true"
    events:
      - "payment-failed"

  - id: "refund-payment"
    type: "action"
    description: "Refund payment due to inventory allocation failure"
    transitions:
      - to: "reject-order"
        condition: "true"
    events:
      - "payment-refunded"

  - id: "complete"
    type: "action"
    description: "Complete order fulfillment process"
    events:
      - "order-completed"
```

---

## 2. Customer Onboarding Flow

A customer onboarding process with document verification and approval workflow.

```yaml
id: "customer-onboarding"
name: "Customer Onboarding"
description: "Onboard new customers with identity verification and approval"
version: "1.2.0"

inputs:
  - name: "applicationId"
    type: "okham_core_identifier"
    description: "Customer application identifier"
    required: true
  
  - name: "applicantEmail"
    type: "okham_core_email-address"
    description: "Applicant email address"
    required: true

outputs:
  - name: "customerId"
    type: "okham_core_identifier"
    description: "New customer identifier"
    required: false
  
  - name: "onboardingStatus"
    type: "okham_crm_onboarding-status"
    description: "Final onboarding status"

state:
  type: "okham_crm_onboarding-state"
  initial:
    status: "pending"
    documentsVerified: false
    riskScore: 0

steps:
  - id: "collect-documents"
    type: "event"
    description: "Wait for applicant to submit required documents"
    event:
      eventId: "documents-submitted"
      mode: "wait"
      filter:
        applicationId: "{{inputs.applicationId}}"
    transitions:
      - to: "verify-identity"
        condition: "event.documentsComplete == true"
        priority: 10
      - to: "request-additional-documents"
        condition: "event.documentsComplete == false"
        priority: 5
    timeout: "P7D"

  - id: "request-additional-documents"
    type: "action"
    description: "Request additional documents from applicant"
    transitions:
      - to: "collect-documents"
        condition: "true"
    events:
      - "additional-documents-requested"

  - id: "verify-identity"
    type: "action"
    description: "Verify applicant identity using third-party service"
    action:
      operation: "verify-identity"
      parameters:
        applicationId: "{{inputs.applicationId}}"
        documents: "{{state.submittedDocuments}}"
    transitions:
      - to: "assess-risk"
        condition: "outputs.verified == true"
        priority: 10
      - to: "manual-review"
        condition: "outputs.verified == false && outputs.confidence < 0.5"
        priority: 8
      - to: "reject-application"
        condition: "outputs.verified == false && outputs.confidence >= 0.5"
        priority: 5
    retry:
      maxAttempts: 2
      backoff: "fixed"
      delay: "PT10S"
    timeout: "PT2M"

  - id: "assess-risk"
    type: "action"
    description: "Assess customer risk profile"
    transitions:
      - to: "auto-approve"
        condition: "outputs.riskScore < 30"
        description: "Low risk, auto-approve"
        priority: 10
      - to: "manual-review"
        condition: "outputs.riskScore >= 30 && outputs.riskScore < 70"
        description: "Medium risk, manual review required"
        priority: 8
      - to: "reject-application"
        condition: "outputs.riskScore >= 70"
        description: "High risk, reject"
        priority: 5

  - id: "manual-review"
    type: "event"
    description: "Wait for manual review decision"
    event:
      eventId: "review-completed"
      mode: "wait"
      filter:
        applicationId: "{{inputs.applicationId}}"
    transitions:
      - to: "auto-approve"
        condition: "event.decision == 'approved'"
        priority: 10
      - to: "reject-application"
        condition: "event.decision == 'rejected'"
        priority: 5
    timeout: "P3D"

  - id: "auto-approve"
    type: "action"
    description: "Automatically approve customer application"
    action:
      operation: "create-customer-account"
      parameters:
        applicationId: "{{inputs.applicationId}}"
        email: "{{inputs.applicantEmail}}"
    transitions:
      - to: "send-welcome-email"
        condition: "true"
    events:
      - "customer-approved"

  - id: "send-welcome-email"
    type: "action"
    description: "Send welcome email to new customer"
    transitions:
      - to: "complete"
        condition: "true"
    events:
      - "welcome-email-sent"

  - id: "reject-application"
    type: "action"
    description: "Reject customer application"
    action:
      operation: "reject-application"
      parameters:
        applicationId: "{{inputs.applicationId}}"
        reason: "{{outputs.rejectionReason}}"
    transitions:
      - to: "complete"
        condition: "true"
    events:
      - "application-rejected"

  - id: "complete"
    type: "action"
    description: "Complete onboarding process"
    events:
      - "onboarding-completed"
```

---

## 3. Invoice Approval Flow

A multi-level invoice approval workflow with escalation.

```yaml
id: "invoice-approval"
name: "Invoice Approval"
description: "Multi-level approval workflow for invoices"
version: "2.1.0"

inputs:
  - name: "invoiceId"
    type: "okham_core_identifier"
    description: "Invoice identifier"
    required: true
  
  - name: "amount"
    type: "okham_core_money"
    description: "Invoice amount"
    required: true
  
  - name: "submitterId"
    type: "okham_core_identifier"
    description: "Employee who submitted invoice"
    required: true

outputs:
  - name: "approvalStatus"
    type: "okham_finance_approval-status"
    description: "Final approval status"
  
  - name: "approvedBy"
    type: "okham_core_identifier"
    description: "Identifier of approver"
    required: false

state:
  type: "okham_finance_approval-state"
  initial:
    currentLevel: 1
    approvers: []
    comments: []

policies:
  - "invoice-approval-policy"
  - "financial-compliance-policy"

steps:
  - id: "determine-approval-level"
    type: "decision"
    description: "Determine required approval level based on amount"
    transitions:
      - to: "manager-approval"
        condition: "inputs.amount < 1000"
        description: "Under $1000, manager approval only"
        priority: 10
      - to: "director-approval"
        condition: "inputs.amount >= 1000 && inputs.amount < 10000"
        description: "$1000-$10000, director approval required"
        priority: 8
      - to: "executive-approval"
        condition: "inputs.amount >= 10000"
        description: "Over $10000, executive approval required"
        priority: 5

  - id: "manager-approval"
    type: "event"
    description: "Wait for manager approval"
    event:
      eventId: "invoice-reviewed"
      mode: "wait"
      filter:
        invoiceId: "{{inputs.invoiceId}}"
        reviewerRole: "manager"
    transitions:
      - to: "approve-invoice"
        condition: "event.decision == 'approved'"
        priority: 10
      - to: "reject-invoice"
        condition: "event.decision == 'rejected'"
        priority: 8
      - to: "request-clarification"
        condition: "event.decision == 'clarification-needed'"
        priority: 5
    timeout: "P2D"

  - id: "director-approval"
    type: "event"
    description: "Wait for director approval"
    event:
      eventId: "invoice-reviewed"
      mode: "wait"
      filter:
        invoiceId: "{{inputs.invoiceId}}"
        reviewerRole: "director"
    transitions:
      - to: "approve-invoice"
        condition: "event.decision == 'approved'"
        priority: 10
      - to: "reject-invoice"
        condition: "event.decision == 'rejected'"
        priority: 8
      - to: "escalate-to-executive"
        condition: "event.decision == 'escalate'"
        priority: 6
      - to: "request-clarification"
        condition: "event.decision == 'clarification-needed'"
        priority: 5
    timeout: "P3D"

  - id: "executive-approval"
    type: "event"
    description: "Wait for executive approval"
    event:
      eventId: "invoice-reviewed"
      mode: "wait"
      filter:
        invoiceId: "{{inputs.invoiceId}}"
        reviewerRole: "executive"
    transitions:
      - to: "approve-invoice"
        condition: "event.decision == 'approved'"
        priority: 10
      - to: "reject-invoice"
        condition: "event.decision == 'rejected'"
        priority: 5
    timeout: "P5D"

  - id: "escalate-to-executive"
    type: "action"
    description: "Escalate invoice to executive level"
    transitions:
      - to: "executive-approval"
        condition: "true"
    events:
      - "invoice-escalated"

  - id: "request-clarification"
    type: "action"
    description: "Request clarification from submitter"
    action:
      operation: "request-clarification"
      parameters:
        invoiceId: "{{inputs.invoiceId}}"
        submitterId: "{{inputs.submitterId}}"
        questions: "{{event.clarificationQuestions}}"
    transitions:
      - to: "wait-for-clarification"
        condition: "true"
    events:
      - "clarification-requested"

  - id: "wait-for-clarification"
    type: "event"
    description: "Wait for submitter to provide clarification"
    event:
      eventId: "clarification-provided"
      mode: "wait"
      filter:
        invoiceId: "{{inputs.invoiceId}}"
    transitions:
      - to: "determine-approval-level"
        condition: "true"
    timeout: "P3D"

  - id: "approve-invoice"
    type: "action"
    description: "Approve invoice for payment"
    action:
      operation: "approve-invoice"
      parameters:
        invoiceId: "{{inputs.invoiceId}}"
        approvedBy: "{{event.reviewerId}}"
    transitions:
      - to: "schedule-payment"
        condition: "true"
    events:
      - "invoice-approved"

  - id: "schedule-payment"
    type: "action"
    description: "Schedule invoice payment"
    transitions:
      - to: "complete"
        condition: "true"
    events:
      - "payment-scheduled"

  - id: "reject-invoice"
    type: "action"
    description: "Reject invoice"
    action:
      operation: "reject-invoice"
      parameters:
        invoiceId: "{{inputs.invoiceId}}"
        rejectedBy: "{{event.reviewerId}}"
        reason: "{{event.rejectionReason}}"
    transitions:
      - to: "complete"
        condition: "true"
    events:
      - "invoice-rejected"

  - id: "complete"
    type: "action"
    description: "Complete approval process"
    events:
      - "approval-completed"
```

---

## 4. Incident Response Flow

An IT incident response workflow with automated and manual steps.

```yaml
id: "incident-response"
name: "Incident Response"
description: "Automated incident detection, triage, and resolution workflow"
version: "1.0.0"

inputs:
  - name: "incidentId"
    type: "okham_core_identifier"
    description: "Incident identifier"
    required: true
  
  - name: "severity"
    type: "okham_ops_severity-level"
    description: "Incident severity"
    required: true

outputs:
  - name: "resolutionStatus"
    type: "okham_ops_resolution-status"
    description: "Final resolution status"
  
  - name: "resolutionTime"
    type: "okham_core_duration"
    description: "Time to resolution"

state:
  type: "okham_ops_incident-state"
  initial:
    status: "open"
    assignedTo: null
    attempts: 0

steps:
  - id: "classify-incident"
    type: "action"
    description: "Classify incident type and impact"
    action:
      operation: "classify-incident"
      parameters:
        incidentId: "{{inputs.incidentId}}"
        severity: "{{inputs.severity}}"
    transitions:
      - to: "auto-remediate"
        condition: "outputs.classification == 'known-issue'"
        priority: 10
      - to: "assign-to-team"
        condition: "outputs.classification == 'new-issue'"
        priority: 5
    timeout: "PT1M"

  - id: "auto-remediate"
    type: "action"
    description: "Attempt automated remediation"
    action:
      operation: "execute-remediation"
      parameters:
        incidentId: "{{inputs.incidentId}}"
        remediationPlan: "{{outputs.remediationPlan}}"
    transitions:
      - to: "verify-resolution"
        condition: "outputs.executed == true"
        priority: 10
      - to: "assign-to-team"
        condition: "outputs.executed == false"
        priority: 5
    retry:
      maxAttempts: 2
      backoff: "fixed"
      delay: "PT30S"
    events:
      - "auto-remediation-attempted"

  - id: "verify-resolution"
    type: "action"
    description: "Verify incident is resolved"
    transitions:
      - to: "close-incident"
        condition: "outputs.resolved == true"
        priority: 10
      - to: "assign-to-team"
        condition: "outputs.resolved == false"
        priority: 5
    timeout: "PT2M"

  - id: "assign-to-team"
    type: "decision"
    description: "Assign to appropriate team based on severity"
    transitions:
      - to: "escalate-to-oncall"
        condition: "inputs.severity == 'critical' || inputs.severity == 'high'"
        priority: 10
      - to: "create-ticket"
        condition: "inputs.severity == 'medium' || inputs.severity == 'low'"
        priority: 5

  - id: "escalate-to-oncall"
    type: "action"
    description: "Page on-call engineer"
    transitions:
      - to: "wait-for-acknowledgment"
        condition: "true"
    events:
      - "oncall-paged"

  - id: "wait-for-acknowledgment"
    type: "event"
    description: "Wait for on-call engineer to acknowledge"
    event:
      eventId: "incident-acknowledged"
      mode: "wait"
      filter:
        incidentId: "{{inputs.incidentId}}"
    transitions:
      - to: "manual-resolution"
        condition: "true"
    timeout: "PT15M"

  - id: "create-ticket"
    type: "action"
    description: "Create support ticket for team"
    transitions:
      - to: "manual-resolution"
        condition: "true"
    events:
      - "ticket-created"

  - id: "manual-resolution"
    type: "event"
    description: "Wait for manual resolution"
    event:
      eventId: "incident-resolved"
      mode: "wait"
      filter:
        incidentId: "{{inputs.incidentId}}"
    transitions:
      - to: "close-incident"
        condition: "event.resolved == true"
        priority: 10
      - to: "escalate-further"
        condition: "event.resolved == false && state.attempts < 3"
        priority: 5
    timeout: "P1D"

  - id: "escalate-further"
    type: "action"
    description: "Escalate to senior team"
    transitions:
      - to: "manual-resolution"
        condition: "true"
    events:
      - "incident-escalated"

  - id: "close-incident"
    type: "action"
    description: "Close resolved incident"
    transitions:
      - to: "complete"
        condition: "true"
    events:
      - "incident-closed"

  - id: "complete"
    type: "action"
    description: "Complete incident response"
    events:
      - "response-completed"
```

---

## 5. Notes on Examples

### 5.1 Non-Normative

These examples are **non-normative** and provided for illustration only. Implementations MAY vary in structure while remaining conformant to the OBC specification.

### 5.2 Composition Patterns

Examples demonstrate:
- **Sequential flows** — Steps execute in order
- **Conditional branching** — Decisions based on state/outputs
- **Subflow composition** — Flows invoking other flows
- **Event-driven steps** — Waiting for or emitting events
- **Error handling** — Retry logic and failure paths
- **Escalation** — Multi-level approval and escalation

### 5.3 Integration with OKHAM Core

Examples show integration with:
- **OTC** — Typed inputs, outputs, and state
- **OEC** — Events emitted at key milestones
- **OPC** — Policies governing flow execution

### 5.4 Real-World Usage

In practice:
- Flows are typically stored in a flow registry
- Execution engines interpret flow definitions
- State is persisted between steps
- Events enable observability and audit trails
- Policies enforce business rules and compliance

### 5.5 Condition Expressions

Examples use simple comparison expressions for clarity. Real implementations may support:
- JSONPath expressions
- Custom expression languages
- Complex boolean logic

---

## 6. References

- [OBC Specification](SPECIFICATION.md)
- [OBC Schema](SCHEMA.md)
- [OTC Examples](../otc/EXAMPLES.md)
- [OEC Examples](../oec/EXAMPLES.md)
- [OPC Examples](../opc/EXAMPLES.md)
