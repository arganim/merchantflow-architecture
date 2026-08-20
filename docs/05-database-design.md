# Database Design

## 1. Purpose and Scope

This document explains the database-design principles behind MerchantFlow. It focuses on ownership, integrity, historical preservation, migration safety, and the operating characteristics of a local desktop database.

It is a sanitized architecture description. Production table names, columns, indexes, migration scripts, database paths, customer data, and commercially sensitive constraints are not published.

## 2. Design Goals

The persistence model is designed to provide:

- Reliable local transactions
- Explicit referential integrity
- Stable internal identities
- Protection of historical business records
- Controlled schema evolution with Flyway
- Deterministic duplicate prevention
- Predictable query behavior for desktop workflows
- Testable repository contracts
- Backup-aware operation
- Clear separation between persistence models and external payloads

## 3. Storage Model

MerchantFlow uses SQLite as the local relational store and Flyway for versioned schema evolution. Spring Data JPA provides repository infrastructure, while application services own transaction boundaries.

The database belongs to one application installation. It is not exposed directly to marketplaces, browser clients, or external reporting tools.

## 4. Conceptual Data Areas

| Data area | Representative records | Ownership rule |
| --- | --- | --- |
| Customer master data | Customer identity, contact details, provenance, activity state | Mutable through controlled customer use cases |
| Address history | Customer address versions and validity metadata | Earlier versions remain attributable |
| Product master data | Commercial, tax, origin, weight, and operational attributes | Changes affect future work, not accepted history |
| Orders | Order header, source, lifecycle, totals, and transaction context | Order aggregate owns the accepted transaction |
| Order lines | Quantity, price, tax, and product snapshot context | Protected as part of the order record |
| Order addresses | Billing and shipping records used by the order | Preserved independently of current customer data |
| Documents | Type, business identity, generation state, and attribution | Must reference an eligible transaction |
| Shipping output | Export identity, validation state, and delivery metadata | Duplicate delivery is controlled |
| External references | Channel scope and external business identity | Unique inside the source scope |
| Configuration | Non-secret operational settings and selected modes | Accessed through controlled configuration services |
| Migration history | Applied schema versions and checksums | Managed by Flyway, not application use cases |

These names describe concepts, not the production schema.

## 5. Identifier Strategy

Internal identifiers are stable and independent of changeable business data.

The design separates:

- Internal primary identity
- Human-readable business numbers
- External channel identifiers
- Natural search attributes

Names, email addresses, document numbers, and external order numbers are not interchangeable forms of identity. External uniqueness always includes the source boundary.

## 6. Relationship Design

Relationships reflect aggregate ownership rather than unrestricted object navigation.

- A customer can own several address versions.
- A customer can be associated with several orders.
- An order owns one or more order lines.
- An order owns its transaction-address records.
- A product can be referenced by many historical order lines.
- An order can produce several controlled document or shipping records.
- An order may carry one or more source-scoped integration references.

Foreign-key behavior is chosen to protect business history. Cascade operations are used only when aggregate ownership and deletion semantics are unambiguous.

## 7. Historical Preservation

The database distinguishes current master data from accepted transaction data.

### 7.1 Addresses

Customer address changes create or select an appropriate version. Existing orders retain their historical billing and shipping context.

### 7.2 Products and prices

Product records support future operations. Accepted order lines preserve the values needed to reconstruct totals and documents.

### 7.3 Documents

Generated business documents remain attributable to the underlying transaction and generation event. Corrections do not erase the existence of the original operation.

## 8. Integrity Constraints

Database constraints complement domain validation. They protect invariants that remain valid regardless of the calling workflow.

Conceptual examples include:

- Required relationships cannot be null.
- Quantity and monetary fields use valid ranges and representations.
- Source-scoped external identities cannot be duplicated.
- Order-address roles use an allowed value set.
- Lifecycle fields use known values.
- Version or audit metadata follows required consistency rules.

Domain policies still decide business meaning. A database constraint is the last safety boundary, not a replacement for explicit application results.

## 9. Index Strategy

Indexes are selected from measured access paths and integrity needs.

Typical index categories include:

- Internal and foreign-key lookups
- Source plus external-reference uniqueness
- Customer search attributes
- Order lifecycle and business-date filters
- Document and shipping attribution
- Operational reporting filters

The design avoids publishing production index names or exact query plans. Indexes are reviewed when migrations or repository queries change.

## 10. Transaction Boundaries

Application services own transactions for complete use cases.

A typical write operation:

1. Loads the evidence required for the decision.
2. Validates domain and application preconditions.
3. Builds an accepted mutation plan.
4. Writes related records within one transaction where appropriate.
5. Commits only a complete valid state.
6. Performs non-transactional external side effects in a deliberate order.

Repository methods do not define business transactions independently.

## 11. SQLite Operating Model

SQLite fits the desktop-first deployment because it provides an embedded relational database without a separately administered server.

The architecture accounts for:

- Bounded local write concurrency
- Short, deliberate transactions
- Explicit handling of busy or locked conditions
- Reliable filesystem and storage-path selection
- Backup coordination
- Controlled application shutdown
- Migration before normal business use

The design does not assume unrestricted concurrent writes from several application nodes.

## 12. Schema Evolution with Flyway

Every production schema change is represented by a versioned migration. Existing installations are upgraded forward rather than recreated silently.

Migration principles include:

- Append-only version history after release
- Deterministic migration order
- Reviewable DDL and data transformations
- Explicit defaults and backfill strategy
- Validation before normal application startup
- Recovery guidance for failed upgrades
- Regression tests from representative earlier schema states

Published portfolio documentation does not include the private migration scripts.

## 13. Migration Testing

Migration tests verify more than successful startup.

They cover:

- Fresh database creation
- Upgrade from supported historical versions
- Preservation of existing customer and order data
- Correct creation of new constraints and indexes
- Safe backfill of required values
- Repeat startup after a completed migration
- Controlled failure for unsupported or inconsistent states

## 14. JPA Mapping Boundaries

Persistence mappings are infrastructure concerns. Domain decisions must not depend on lazy-loading accidents, automatic cascade behavior, or entity equality tied to mutable fields.

The design therefore favors:

- Explicit repository queries for use-case needs
- Controlled aggregate loading
- Stable identity semantics
- Deliberate cascade and orphan behavior
- Mapping tests for important relationships
- DTO boundaries for UI and external connectors

## 15. Deletion and Retention

Business history is not deleted merely because current master data is no longer active.

The architecture distinguishes:

- Deactivation of reusable master data
- Correction through an explicit business operation
- Retention of transaction and document evidence
- Privacy-driven deletion or anonymization under a separate controlled policy

No unrestricted UI action cascades through historical orders and documents.

## 16. Backup and Recovery

Backup is treated as an application operation, not as blind copying during an unknown write state.

The conceptual strategy includes:

- Known database location selected by configuration
- Coordinated or verified snapshot creation
- Clear backup metadata
- Recovery validation
- Diagnostics that identify the active storage without exposing sensitive data unnecessarily

Exact paths, schedules, and operational credentials remain private.

## 17. Privacy and Security

The database contains personal and commercial information in the private application. Access is limited to the application and authorized local operation.

Public portfolio material excludes:

- Real customer or order records
- Database copies and exports
- Production paths and file names
- Credentials, tokens, and license material
- Full schemas and proprietary constraints

Logs and diagnostics must avoid unnecessary personal data and secrets.

## 18. Repository and Regression Testing

Repository tests verify query contracts, relationship loading, uniqueness behavior, and transaction outcomes against a real database engine where relevant.

Regression coverage focuses on:

- Duplicate prevention
- Historical-data preservation
- Address and order ownership
- Reporting correctness
- Migration compatibility
- Failure behavior under invalid state

## 19. Deliberate Non-Goals

The persistence architecture does not provide:

- A public database API
- Direct editing by external tools
- Multi-region or distributed writes
- Schema recreation as a normal upgrade strategy
- Hidden destructive cascade behavior
- External payloads stored as authoritative domain state without validation

## 20. Related Documentation

- [Domain Model](04-domain-model.md)
- [System Architecture](03-system-architecture.md)
- [Conceptual Domain Diagram](../diagrams/domain-model.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
