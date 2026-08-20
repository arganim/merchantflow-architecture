# Testing Strategy

## 1. Purpose and Scope

This document describes the conceptual testing strategy behind MerchantFlow. It explains how business rules, persistence, migrations, integrations, JavaFX workflows, and regression behavior are verified at appropriate boundaries.

The public case study does not include private source code, test fixtures, database snapshots, customer data, external credentials, or complete production test suites.

## 2. Quality Principles

The strategy follows these principles:

- Test contracts and observable behavior
- Keep business rules independent of JavaFX and infrastructure
- Use the narrowest effective test level
- Verify failure behavior as deliberately as success behavior
- Preserve historical data through regression and migration tests
- Use synthetic and deterministic test data
- Treat flaky tests as defects
- Keep CI evidence independent of AI-generated implementation
- Review test quality, not only test quantity

## 3. Layered Test Model

| Test layer | Primary subject | Main evidence |
| --- | --- | --- |
| Unit | Pure calculations, normalization, and small components | Deterministic input-to-output behavior |
| Policy | Domain decisions and state transitions | Decision tables, invariants, and fail-closed results |
| Repository | Queries, mappings, relationships, and constraints | Persistence contract against the selected database engine |
| Application service | Use-case orchestration and transaction boundaries | Complete accepted or rejected operation result |
| Integration | Collaboration across Spring, adapters, and infrastructure | Correct boundary translation and failure handling |
| Migration | Fresh schema and supported historical upgrades | Data preservation and deterministic evolution |
| UI/headless | JavaFX interaction and presentation integration | User intent, state, and feedback behavior |
| Regression | Previously observed risks across layers | Protection against recurring defects |

The [Testing Strategy Diagram](../diagrams/testing-strategy.md) shows how these layers converge on one release decision.

## 4. Unit Tests

Unit tests cover small deterministic behavior without starting the complete application context.

Representative subjects include:

- Text and address normalization
- Monetary and quantity calculations
- Mapping helpers
- Date and time boundary logic
- Error classification
- Formatting rules that do not require rendering infrastructure

Unit tests should remain fast enough to run continuously during development.

## 5. Domain-Policy Tests

Policy tests verify business decisions with explicit scenarios.

Examples include:

- Customer identity evidence
- Address-version decisions
- Duplicate-order outcomes
- Valid and invalid lifecycle transitions
- Document eligibility
- Capability allow and deny results
- Ambiguity and fail-closed behavior

Decision-oriented tests use named scenarios and assert structured outcomes rather than private implementation calls.

## 6. Application-Service Tests

Application-service tests verify use-case orchestration.

They check that the service:

- Loads all required evidence
- Calls the correct policies
- Builds a plan before mutation
- Opens the intended transaction boundary
- Coordinates repositories and adapters in the required order
- Returns a structured result
- Avoids partial state after failure

Controlled fakes can isolate external side effects while preserving the application contract.

## 7. Repository Tests

Repository tests verify persistence behavior against SQLite where database-specific behavior matters.

Coverage includes:

- Query results and ordering
- Required relationship loading
- Source-scoped uniqueness
- Mapping of identifiers and value types
- Constraint behavior
- Transaction visibility
- Historical-record preservation

Mocks do not replace repository tests for behavior owned by the database or ORM mapping.

## 8. Migration Tests

Migration tests verify schema evolution from supported states.

Important scenarios include:

- Fresh database creation
- Upgrade from representative historical versions
- Backfill of required values
- Creation of constraints and indexes
- Preservation of customer, order, and document relationships
- Repeated startup after successful migration
- Controlled failure for inconsistent or unsupported state

Test fixtures are synthetic and stripped of production information.

## 9. Integration Tests

Integration tests verify collaboration across multiple real boundaries.

Representative scenarios include:

- Spring context composition
- Service and repository collaboration
- Transaction commit and rollback
- Connector mapping with local fixtures
- Document-generation contracts
- File and export boundary behavior
- License-provider integration through controlled evidence

External production services are replaced by fakes, stubs, or dedicated test environments.

## 10. Connector Contract Tests

Connectors translate untrusted external formats into internal requests.

Contract tests verify:

- Required and optional field mapping
- Unsupported or missing values
- Source identity preservation
- Technical error translation
- Payload version compatibility where supported
- No business mutation during parsing

Real account identifiers and payloads are not stored in public fixtures.

## 11. JavaFX and Headless Tests

JavaFX tests focus on presentation responsibilities:

- User intent reaches the correct application use case
- View state reflects structured results
- Controls are enabled or disabled consistently
- Validation feedback is visible and actionable
- Background work does not block the application thread incorrectly
- Windows and application threads are cleaned up after tests

CI can provide a virtual display such as Xvfb for supported headless execution.

## 12. Document and Export Tests

Documents and exports are business outputs, not arbitrary UI files.

Tests can verify:

- Eligibility checks occur before rendering
- Required transaction values are used
- Output structure is deterministic
- Duplicate-output policy is respected
- Errors do not produce misleading success state
- Personal data is not included beyond the approved business purpose

Public tests do not include real invoices, labels, customer addresses, or carrier accounts.

## 13. Security and Privacy Tests

Security-focused tests verify architecture controls such as:

- Protected operations require capability decisions
- Invalid license evidence fails closed
- Secrets are not written to logs
- Untrusted input is validated before persistence
- Paths and file names remain within approved boundaries
- Personal data is excluded from diagnostics where unnecessary
- Public-repository checks reject known sensitive patterns

Testing reduces risk but is not a standalone security certification.

## 14. Failure-Path Testing

Failure behavior is part of the contract.

Tests deliberately exercise:

- Invalid and incomplete input
- Ambiguous customer identity
- Duplicate external order conflicts
- Database constraint failure
- Transaction rollback
- Locked or unavailable local storage
- Connector timeout or malformed response
- Document, email, or export delivery failure
- Unsupported migration state

A failure test verifies both the reported result and the absence of unintended mutation.

## 15. Concurrency and Idempotency Tests

The desktop model has bounded concurrency, but background imports and repeated commands can overlap.

Tests cover:

- Repeated user action
- Duplicate connector delivery
- Two attempts using the same external identity
- Rechecking state before commit
- Safe retry after a technical failure
- No second accepted transaction after idempotent replay

## 16. Test Data Strategy

Test data is synthetic, minimal, and purpose-specific.

Principles include:

- No copied production databases
- No real customer or partner information
- Explicit builders or fixtures
- Stable dates, identifiers, and currencies where needed
- Clear boundary values
- Separate valid, invalid, and ambiguous scenarios
- Easy diagnosis when a fixture fails

## 17. Determinism

Tests control sources of nondeterminism:

- Time and clocks
- Random identifiers
- Locale and time zone
- File-system locations
- External network calls
- Thread scheduling where possible
- Database initialization state

An unstable dependency is wrapped behind a controllable boundary.

## 18. Test Isolation

Each test owns its relevant state and cleans up after execution.

Isolation prevents:

- Order-dependent results
- Shared database contamination
- Leaked JavaFX threads or windows
- Reused output files
- Hidden dependency on local developer configuration

## 19. Regression Strategy

When a defect is fixed, a focused regression test captures the smallest stable contract that would have detected it.

Regression suites are organized around risks such as:

- Identity and duplicate resolution
- Historical-data preservation
- State transitions
- Document attribution
- Database upgrades
- Capability enforcement
- External mapping

The number of tests is an engineering signal, not proof of correctness by itself.

## 20. Flaky-Test Policy

A flaky test is treated as a defect in the test or system boundary.

The response includes:

- Record the failing scenario
- Identify time, concurrency, environment, or cleanup dependence
- Reproduce with controlled inputs
- Fix or quarantine only with explicit ownership
- Restore the required gate promptly

Repeated blind reruns are not considered a valid acceptance strategy.

## 21. Coverage and Mutation Evidence

Coverage can identify unexecuted areas but does not measure assertion quality or business completeness.

Where useful, mutation or contract review can reveal tests that execute code without detecting incorrect behavior. Targets are risk-based rather than a universal percentage used in isolation.

## 22. CI Execution Strategy

Fast unit and policy tests provide early feedback. Repository, integration, migration, and JavaFX tests execute as required merge gates.

Independent groups may run in parallel. All required results converge before the pull request is accepted.

## 23. AI-Assisted Test Development

AI can propose scenarios, fixtures, assertions, and refactoring, but generated tests require human review.

Review checks that a test:

- Represents a real contract
- Can fail for the intended reason
- Does not merely mirror the implementation
- Uses safe synthetic data
- Covers meaningful edge cases
- Remains maintainable

CI provides independent execution evidence; AI does not approve its own output.

## 24. Acceptance Criteria

A change is ready to merge when:

- Required focused tests pass
- Required regression groups pass
- Migration behavior is validated when the schema changes
- New failure behavior is covered
- Test data remains synthetic and safe
- No unexplained flaky result remains
- Human review confirms architecture and acceptance criteria

## 25. Public Abstraction Boundary

This document deliberately excludes:

- Private test source code and fixtures
- Production database snapshots
- Real customer, license, or marketplace data
- Internal coverage reports and defect metrics
- Security test payloads that could enable bypass
- Production CI workflow definitions

## 26. Related Documentation

- [CI/CD Pipeline](09-ci-cd-pipeline.md)
- [System Architecture](03-system-architecture.md)
- [Database Design](05-database-design.md)
- [Security and Privacy](11-security-and-privacy.md)
- [Testing Strategy Diagram](../diagrams/testing-strategy.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
