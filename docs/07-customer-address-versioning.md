# Customer and Address Versioning

## 1. Purpose and Scope

This document explains how MerchantFlow separates customer identity, mutable customer master data, address versions, and the historical address context of an order.

The design addresses a common commerce problem: people move, contact data changes, external channels provide inconsistent spelling, and historical orders must still remain correct and attributable.

Production matching rules, scoring thresholds, schemas, personal data, and customer-specific exceptions are intentionally excluded.

## 2. Design Goals

The customer and address model is designed to provide:

- Stable internal customer identity
- Explicit evidence for matching
- Safe handling of ambiguous records
- Preserved address history
- Independent billing and shipping roles
- Immutable or protected order-address context
- Controlled synchronization with external sources
- Traceable corrections
- Privacy-aware diagnostics and testing

## 3. Core Distinctions

| Concept | May change? | Historical responsibility |
| --- | --- | --- |
| Customer identity | Stable | Connects the long-lived business relationship |
| Customer contact data | Yes, through controlled updates | Previous values may remain attributable where required |
| Customer address version | New versions can be created | Earlier versions are preserved |
| Order address | Protected after transaction acceptance | Records billing or shipping context used by that order |
| External customer reference | Source-dependent | Supports synchronization but does not replace local identity |

The central rule is that current master data and historical transaction data serve different purposes and must not overwrite one another.

## 4. Customer Identity

A customer has a stable internal identity that is independent of changeable names, email addresses, telephone numbers, and postal addresses.

Business attributes support search and resolution, but none is assumed globally unique without an explicit domain rule.

Examples:

- Two different people can share a name.
- A household can share an address.
- An email address can change or be reused.
- One organization can have several contacts and delivery locations.
- External channels can format the same data differently.

## 5. Identity Evidence

Customer resolution evaluates a set of evidence rather than one convenient field.

Conceptual evidence categories include:

- Verified internal or source-scoped reference
- Normalized email or telephone match
- Name and organization context
- Address and country context
- Previous accepted relationship
- Conflicting evidence

Evidence strength and conflicts are evaluated explicitly. The exact private decision table is not published.

## 6. Normalization Boundary

Normalization produces comparable values but does not decide identity.

Examples include:

- Whitespace and case normalization
- Unicode-aware text handling
- Country and postal formatting
- Telephone comparison representation
- Email comparison representation
- Separation of person, organization, and address fields

Original values required for business use remain available. A normalized match is supporting evidence, not automatic permission to merge.

## 7. Resolution Outcomes

The customer-resolution policy returns structured outcomes:

- Verified existing customer
- New customer required
- Existing customer with new contact or address version
- Review required because evidence is incomplete
- Blocked because candidates conflict

The caller receives the outcome and explanation category. It does not receive permission to choose a convenient candidate silently.

## 8. Ambiguity Policy

MerchantFlow follows a fail-closed policy for identity ambiguity.

If the available evidence cannot establish a safe customer binding, the workflow:

- Does not modify an existing customer
- Does not attach the order to the first search result
- Does not merge candidates automatically
- Returns a review or block result
- Preserves enough non-sensitive diagnostic context to explain the decision

Creating a new customer can also be blocked when doing so would conflict with a strong external identity.

## 9. Address Ownership

A customer address version belongs to the customer aggregate. It represents a known location during a period of the business relationship.

An address version can include conceptually:

- Address role or intended use
- Normalized comparison form
- Original accepted presentation
- Source and creation metadata
- Active or superseded state

The exact database representation remains private.

## 10. Address-Version Decision

When new address input arrives, the policy compares it with customer-owned versions.

Conceptual outcomes include:

- Reuse the verified equivalent version
- Create a new address version
- Preserve separate billing and shipping versions
- Reject incomplete or invalid input
- Require review when ownership is ambiguous

Updating an address means selecting or creating an appropriate version, not rewriting every historical occurrence.

## 11. Order-Owned Address Context

An accepted order preserves the billing and shipping values used for that transaction. This context belongs to the order history.

The order-address record protects:

- The address role
- The accepted presentation
- The relationship to the order
- The point-in-time business context
- Attribution for later documents and shipping output

Later changes to customer master data do not alter this record silently.

## 12. Billing and Shipping Roles

Billing and shipping addresses are evaluated independently.

They can be:

- The same accepted location
- Different customer-owned versions
- A one-time transaction destination where policy permits
- Incomplete or conflicting and therefore blocked

The role remains explicit so documents and fulfilment do not infer intent from field equality.

## 13. External Channel Input

External channels can supply customer and address data, but they do not control local identity.

The integration boundary:

1. Preserves source-scoped references.
2. Maps fields into an internal resolution request.
3. Normalizes comparison values.
4. Evaluates customer evidence.
5. Evaluates billing and shipping addresses.
6. Builds an accepted plan or returns a review/block outcome.

Raw payload structure does not leak into the domain model.

## 14. Plan Before Mutation

Resolution returns a plan before any customer or address record is changed.

The plan can describe:

- Existing customer to reuse
- New customer to create
- Contact values to update under policy
- Address versions to reuse or create
- Transaction-address values to preserve
- Conflicts that prevent mutation

The application service applies only an accepted plan within the order transaction.

## 15. Corrections

Corrections are explicit operations with different semantics from ordinary master-data updates.

A correction can require:

- User intent and reason
- Eligibility based on order state
- A new address or document version
- Preservation of the original record
- Audit metadata
- Regeneration rules for affected output

Completed history is not rewritten through a generic customer-edit screen.

## 16. Customer Merge and Separation

Combining customer records is high risk because it can change ownership of orders, addresses, and documents.

If supported, merge or separation is treated as a dedicated reviewed use case with:

- Explicit source and target identities
- Conflict detection
- Preview of affected relationships
- Transactional execution
- Traceable result

This public case study does not expose the production merge rules.

## 17. Concurrency and Duplicate Prevention

Concurrent import and manual work can observe the same candidate before either transaction commits.

Protection can include:

- Rechecking strong identity before commit
- Source-scoped uniqueness constraints
- Short transactions
- Explicit conflict outcomes
- Retry that repeats the full resolution policy

Retry never skips identity or address validation.

## 18. Deactivation and Retention

Deactivating a customer or address version prevents inappropriate future use but does not erase historical orders.

Retention and privacy-driven deletion are separate controlled policies. Business retention, legal duties, and data-subject rights must be balanced explicitly rather than implemented as unrestricted cascading deletion.

## 19. Diagnostics and Privacy

Resolution diagnostics must explain outcomes without exposing unnecessary personal data.

Safe diagnostic categories can state that:

- A strong reference matched
- Several candidates conflicted
- Required evidence was missing
- A new version was created
- A historical binding was preserved

Logs do not need full addresses, complete external payloads, or secrets to be useful.

## 20. Testing Strategy

Representative tests cover:

- Exact verified identity match
- Similar names belonging to different customers
- Changed email or telephone data
- Same customer with a new address
- Separate billing and shipping addresses
- Reuse of an equivalent normalized address
- Preservation of historical order addresses
- Ambiguous candidates and fail-closed behavior
- Concurrent duplicate attempts
- Controlled correction and deactivation

Synthetic data is used for public examples and automated tests.

## 21. Public Abstraction Boundary

This document deliberately excludes:

- Production matching weights and thresholds
- Complete merge and correction rules
- Real customer or address examples
- Exact database constraints and query logic
- Marketplace account identifiers and payloads
- Retention periods tied to a specific business or jurisdiction

## 22. Related Documentation

- [Domain Model](04-domain-model.md)
- [Database Design](05-database-design.md)
- [Order Workflow](06-order-workflow.md)
- [Customer Resolution Diagram](../diagrams/customer-flow.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
