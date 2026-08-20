# Domain Model

## 1. Purpose and Scope

This document describes the conceptual domain model of MerchantFlow. It explains the business concepts, ownership boundaries, identities, historical-data rules, and invariants that guide the private implementation.

The model is intentionally technology-neutral. It does not publish production class names, package structures, database table names, proprietary decision logic, or customer-specific configuration.

## 2. Modelling Goals

The domain model is designed to support:

- Explicit ownership of business records
- Reliable customer and order identity
- Separation of mutable master data from historical transactions
- Controlled order-state transitions
- Traceable document and shipping output
- Safe ingestion of external marketplace orders
- Deterministic duplicate prevention
- Central fail-closed business decisions
- Testable invariants independent of the user interface

## 3. Core Concepts

| Concept | Responsibility | Important boundary |
| --- | --- | --- |
| Customer | Represents the merchant's relationship with a person or organization | A similar name is not sufficient evidence of identity |
| Address version | Represents a current or former customer address | Historical versions are preserved instead of overwritten silently |
| Product | Holds reusable commercial and operational master data | Order history must not change when product master data changes |
| Order | Owns the accepted commercial transaction and its lifecycle | Invalid or ambiguous transitions are blocked |
| Order line | Records the product, quantity, price, and tax context accepted for an order | Transaction values are protected from later master-data changes |
| Order address | Records the billing or shipping address used for the transaction | It belongs to the order history, not to the mutable customer profile |
| Business document | Represents an invoice, delivery note, cancellation, or related output | Documents must be attributable to a valid transaction state |
| Shipping export | Represents validated data prepared for a carrier or shipping workflow | Repeated output must be detectable and controlled |
| External reference | Connects a local order to an external channel identity | External identifiers are scoped by their source system |
| Capability decision | Represents whether an operation is available in the active edition and mode | Uncertain protected states are denied rather than guessed |

## 4. Aggregate Boundaries

MerchantFlow uses aggregate boundaries to define where consistency must be protected.

### 4.1 Customer aggregate

The customer aggregate owns the current relationship-level data and the history of known addresses. It protects identity evidence and prevents accidental merging of different people or organizations.

Typical responsibilities include:

- Stable internal customer identity
- Normalized but non-authoritative search attributes
- Contact information and provenance
- Address-version ownership
- Active or inactive lifecycle markers
- Explicit merge or correction workflows where supported

### 4.2 Order aggregate

The order aggregate owns the accepted transaction. It contains the order lines, transaction addresses, financial context, source information, and lifecycle state required to interpret the order later.

The aggregate protects:

- Required order lines
- Accepted quantities, prices, and tax context
- Billing and shipping history
- Source and duplicate identity
- Valid lifecycle transitions
- Eligibility for booking, cancellation, documents, and export

### 4.3 Product aggregate

The product aggregate owns reusable master data for future operations. Existing order lines retain the values accepted for their transaction even if a product is renamed, repriced, reclassified, or deactivated later.

### 4.4 Output records

Documents and shipping exports are derived from approved business state but remain traceable records in their own right. They capture what was generated, for which transaction, and under which controlled operation.

## 5. Identity Strategy

Internal identity and business identity are different concerns.

- Internal identifiers provide stable references inside the application.
- Business attributes such as names, email addresses, and postal addresses support search and matching but can change.
- External identifiers are meaningful only together with their source channel.
- Document numbers follow controlled business rules and are not reused as database identity.

Customer matching uses explicit evidence. Ambiguous similarity produces a review or block outcome; it does not silently join records.

## 6. Customer and Address History

A customer may have several address versions over time. Updating current master data must not rewrite the address used by an earlier transaction.

The model therefore distinguishes:

1. A customer-owned address version used for current or future work.
2. An order-owned address record that preserves the transaction context.
3. An explicit correction or replacement operation when historical data must be amended lawfully.

This distinction supports document consistency, auditability, and safe synchronization with external channels.

## 7. Order Lines and Snapshots

An order line refers to a product but also records the commercial values accepted when the transaction was created or booked.

Protected transaction values can include:

- Product description used for the order
- Quantity and unit context
- Accepted price
- Tax classification or calculated tax context
- Operational attributes needed for shipping or documents

The exact implementation is private. The architectural rule is that later master-data changes must not silently alter historical totals or documents.

## 8. External Order Boundary

Marketplace data enters MerchantFlow as untrusted transport data. An adapter parses and maps the payload, but the application decides whether it can become a local business record.

Acceptance requires controlled decisions for:

- External source identity
- Duplicate detection
- Customer resolution
- Address normalization
- Product resolution
- Required commercial values
- Initial lifecycle state

An external payload is never authoritative merely because it was downloaded successfully.

## 9. Order Lifecycle

The public model uses conceptual states rather than publishing every production state or transition.

Typical lifecycle categories are:

- Draft or imported
- Validated
- Accepted or booked
- Fulfilment in progress
- Completed
- Cancelled or corrected

Transitions are commands with preconditions. A state value must not be changed directly by UI code, import mapping, or document rendering.

## 10. Business Invariants

Representative invariants include:

- An order must have a stable internal identity.
- An accepted order must contain at least one valid line.
- Historical order values are not overwritten by master-data updates.
- Billing and shipping roles remain explicit.
- External order identity is unique within its source scope.
- Duplicate detection runs before creating a second local transaction.
- A document is generated only for an eligible order state.
- Cancellation preserves traceability to the original transaction.
- Shipping output is validated before delivery to an external workflow.
- A protected capability must be allowed by the central capability decision.
- Ambiguity blocks mutation when correctness cannot be established safely.

These are architectural examples, not a complete publication of the private rule set.

## 11. Decision Results

Domain policies return structured outcomes instead of booleans with hidden meaning.

Conceptual outcomes include:

- Accept the proposed operation
- Create a new customer or address version
- Reuse an existing verified binding
- Require explicit user review
- Reject an invalid transition
- Block because evidence is ambiguous
- Deny because a required capability is unavailable

Structured results make the UI, logs, tests, and transaction handling consistent without moving business logic into presentation code.

## 12. Temporal and Audit Considerations

Important records can carry creation, update, source, and version metadata. Time metadata supports traceability, but it does not replace domain rules.

The model distinguishes:

- When data was received
- When it became accepted business state
- Which source supplied it
- Which version was used by a transaction
- Which controlled operation produced a document or export

## 13. Service Boundaries

Application services coordinate aggregates through explicit use cases. They load required evidence, call domain policies, apply accepted plans inside a transaction, and return a structured result.

Cross-aggregate changes are not performed through unrestricted object navigation. This keeps transaction boundaries visible and avoids accidental cascades.

## 14. Testing Consequences

The domain model leads to focused tests for:

- Customer-match decision tables
- Address-version creation and preservation
- Order-line snapshot behavior
- Duplicate external-order prevention
- Valid and invalid lifecycle transitions
- Document and shipping eligibility
- Fail-closed capability outcomes
- Historical-data preservation after master-data changes

The most important invariants can be tested without JavaFX, SQLite, or a live marketplace.

## 15. Public Abstraction Boundary

This document deliberately excludes:

- Production entity and package names
- Full decision tables and scoring rules
- Exact document-number algorithms
- Commercial pricing or licensing rules
- Real schemas, identifiers, customer records, and external payloads
- Anti-tampering and signing details

## 16. Related Documentation

- [System Architecture](03-system-architecture.md)
- [Database Design](05-database-design.md)
- [Conceptual Domain Diagram](../diagrams/domain-model.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
