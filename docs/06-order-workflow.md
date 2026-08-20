# Order Workflow

## 1. Purpose and Scope

This document describes the conceptual MerchantFlow order workflow from capture or import through validation, acceptance, booking, fulfilment output, and correction.

It explains orchestration and safety boundaries without publishing production states, connector payloads, tax algorithms, document-number rules, or commercially sensitive workflow details.

## 2. Workflow Goals

The order workflow is designed to provide:

- One controlled process for manual and external orders
- Early duplicate detection
- Explicit customer, address, and product resolution
- Protected transaction values
- Valid state transitions
- Atomic local persistence
- Deliberate handling of external side effects
- Traceable documents and shipping output
- Fail-closed behavior for ambiguity and invalid state
- Testable outcomes at every stage

## 3. Entry Channels

| Entry channel | Initial representation | Important control |
| --- | --- | --- |
| Manual entry | User-entered application request | UI validation does not replace domain validation |
| Marketplace import | Connector-specific transport payload | Payload is mapped and normalized before acceptance |
| Repeated synchronization | Previously observed external identity and current payload | Idempotency and duplicate decisions run before mutation |
| Controlled correction | Explicit command against an existing order | Historical traceability and eligibility are preserved |

All channels converge on internal application contracts. Connector models and JavaFX controls do not become domain records directly.

## 4. Workflow Overview

The public workflow contains the following stages:

1. Capture or retrieve input.
2. Map transport-specific fields into an internal request.
3. Normalize values used for comparison and validation.
4. Resolve external identity and detect duplicates.
5. Resolve customer and address decisions.
6. Resolve products and validate order lines.
7. Evaluate commercial and lifecycle preconditions.
8. Build an accepted persistence plan.
9. Commit the complete local transaction.
10. Trigger only eligible documents, messages, or shipping output.

The [Order Flow Diagram](../diagrams/order-flow.md) shows this sequence and its principal block outcomes.

## 5. Capture and Mapping

Input is first converted into an application request that separates transport concerns from business meaning.

Mapping responsibilities can include:

- Reading source fields
- Converting technical types
- Preserving source identity
- Identifying missing required input
- Classifying transport errors
- Recording safe provenance metadata

Mapping must not silently create customers, book orders, or bypass lifecycle rules.

## 6. Normalization

Normalization creates comparable representations while preserving the original accepted business values where required.

Examples include:

- Trimming and canonicalizing user input
- Normalizing country and postal representations
- Standardizing comparison forms for email or telephone data
- Converting quantities and monetary values into defined internal types
- Mapping external status values to an application-level interpretation

Normalization is deterministic and testable. It is not evidence that two records belong to the same customer.

## 7. Duplicate Detection

Duplicate prevention runs before a new local order is created.

Evidence can include:

- Source channel plus external order identity
- Previously accepted synchronization reference
- Existing local transaction identity
- A controlled fingerprint used only as supporting evidence

The strongest rule is source-scoped external identity. Similar names, totals, or addresses alone are insufficient to merge orders.

Conceptual outcomes are:

- New order candidate
- Existing order already accepted
- Existing order requires synchronization review
- Conflicting identity that blocks processing

## 8. Customer Resolution

The order request does not own the customer-matching rules. It delegates to an explicit customer-resolution policy.

That policy can decide to:

- Reuse a verified existing customer
- Create a new customer
- Require user review
- Block an ambiguous match

No fallback silently chooses the first similar record.

## 9. Address Resolution

Billing and shipping roles are evaluated explicitly. Current customer addresses and transaction addresses have different lifecycles.

The workflow may:

- Reuse an appropriate current address version
- Create a new customer address version
- Preserve a distinct billing or shipping role
- Create order-owned historical address records
- Block an invalid or incomplete address

Historical order addresses are not replaced when customer master data changes later.

## 10. Product and Line Resolution

Each order line is validated before the order can be accepted.

Checks can include:

- Product identity or controlled fallback behavior
- Quantity and unit validity
- Price and tax context
- Required operational attributes
- Shipping-related completeness
- Duplicate or conflicting line information

The accepted line captures the transaction values required for future documents and reporting.

## 11. Commercial Validation

Application and domain rules evaluate whether the complete order is coherent.

Representative checks include:

- At least one valid line exists.
- Required customer and address decisions are complete.
- Monetary values use valid representations.
- Totals and line context are internally consistent.
- Source identity is not already bound incorrectly.
- The requested initial state is allowed.
- Required capabilities are available.

The exact pricing and tax rules remain outside this public case study.

## 12. Plan Before Mutation

MerchantFlow separates decision-making from persistence. Validation produces a structured plan before business records are written.

The plan describes conceptually:

- Which existing records are reused
- Which new records are created
- Which historical values are copied into the order
- Which relationships are established
- Which lifecycle transition is requested
- Which operation is blocked and why

Only an accepted plan reaches the mutation phase.

## 13. Transaction Boundary

The local database transaction covers the related state required to accept the order consistently.

Within the transaction, the application can:

- Create or update approved customer master data
- Create required address versions
- Persist the order and lines
- Persist transaction-address records
- Bind source-scoped external references
- Record lifecycle and audit metadata

If a required write fails, the accepted local operation is rolled back rather than leaving a partial business record.

## 14. Lifecycle Transitions

The public workflow uses conceptual lifecycle categories:

- Draft or imported
- Validated
- Accepted or booked
- In fulfilment
- Completed
- Cancelled or corrected

Transitions are explicit commands. They verify the current state, required data, capability decisions, and any prior output before changing the order.

Direct status mutation from the UI, connector mapping, or document code is not permitted.

## 15. Booking

Booking marks a controlled business commitment. It is not equivalent to saving a form.

Booking preconditions can include:

- Complete identity and address decisions
- Valid order lines and totals
- Allowed previous lifecycle state
- Absence of unresolved duplicate conflicts
- Required configuration and capability availability

After booking, protected transaction values cannot be edited as ordinary master data.

## 16. Documents and Fulfilment Output

Documents, printing, email, and shipping exports are side effects derived from accepted business state.

Each output operation verifies:

- Order eligibility
- Required data completeness
- Prior output or duplicate-delivery state
- Selected output configuration
- Attribution to the order and operation

Database success and external delivery success are recorded as separate concerns when they cannot share one transaction.

## 17. Cancellation and Correction

Cancellation is an explicit business operation rather than deletion.

The workflow protects:

- Reference to the original transaction
- Eligibility of the current state
- Attribution of cancellation documents
- Preservation of prior documents and outputs
- Clear result when cancellation cannot proceed

Corrections follow a controlled replacement or versioning path. They do not silently rewrite completed history.

## 18. Idempotency and Retry

Repeated connector delivery or user retry must not create a second accepted order unintentionally.

The workflow uses stable source identity and persisted operation state to distinguish:

- Safe repeat of an already completed request
- Resumable technical failure
- New business operation
- Conflicting payload requiring review

Retry behavior is explicit and bounded. Technical retry does not weaken business validation.

## 19. Failure Handling

Failures are classified into structured categories such as:

- Invalid input
- Ambiguous identity
- Duplicate conflict
- Disallowed lifecycle transition
- Missing capability or configuration
- Persistence failure
- External transport or output failure

Users receive actionable outcomes, while sensitive internal details and external credentials remain protected.

## 20. Concurrency Considerations

The desktop operating model has bounded local concurrency, but repeated clicks, background imports, and overlapping operations can still compete.

Protection includes:

- Transactional uniqueness for external identity
- Rechecking relevant state before commit
- Short transaction boundaries
- Controlled UI action state
- Explicit handling of busy or conflicting outcomes

## 21. Security and Privacy

Order processing handles personal and commercial data. The workflow minimizes propagation of raw payloads and avoids logging unnecessary personal information.

Secrets, connector credentials, production endpoints, customer records, and real order payloads are not part of this repository.

## 22. Testing Strategy

Representative tests cover:

- Manual and external order acceptance
- Mapping and normalization boundaries
- Duplicate and idempotency outcomes
- Customer and address resolution results
- Product and line validation
- Valid and invalid state transitions
- Transaction rollback on failure
- Document and shipping eligibility
- Cancellation traceability
- Retry behavior after technical failure

## 23. Public Abstraction Boundary

This document deliberately excludes:

- Full production state machines
- Real marketplace formats and identifiers
- Proprietary tax, pricing, and numbering rules
- Exact retry schedules and operational thresholds
- Production database and package names
- Customer or transaction examples derived from real data

## 24. Related Documentation

- [Domain Model](04-domain-model.md)
- [Database Design](05-database-design.md)
- [Customer and Address Versioning](07-customer-address-versioning.md)
- [Order Flow Diagram](../diagrams/order-flow.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
