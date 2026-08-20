# MerchantFlow — Architecture Case Study

> A sanitized architecture case study of a Java 21 desktop commerce management platform.

[Deutsche Version](README.de.md)

## About This Repository

MerchantFlow documents the architecture and engineering practices behind a private, production-oriented commerce management platform. The case study was written independently for professional portfolio and recruitment purposes.

The production source code, customer data, credentials, database files, and commercially sensitive rules are intentionally excluded. This repository contains documentation and diagrams only; it is not a runnable application.

## The Product Problem

Small and medium-sized merchants often manage orders, customer records, shipping, accounting documents, and marketplace data across disconnected tools. That creates duplicate work, inconsistent data, weak traceability, and avoidable operational risk.

MerchantFlow explores a desktop-first platform that brings these workflows into one controlled application while preserving historical business records and supporting incremental integration with external sales channels.

## Core Capabilities

- Customer and address management
- Product and inventory-related master data
- Manual and marketplace order processing
- Invoice, delivery note, and cancellation-document generation
- Shipping-data preparation and export
- Accounting-oriented reporting
- Marketplace connector integration
- Database migration and backup-aware operation
- Edition and license-based capability control
- Diagnostics, validation, and audit-friendly workflows

## Technology Stack

| Area | Technology |
| --- | --- |
| Language | Java 21 |
| Desktop UI | JavaFX |
| Application framework | Spring Boot |
| Build and dependency management | Maven |
| Persistence | SQLite and Spring Data JPA |
| Database migrations | Flyway |
| Testing | JUnit 5 and integration tests |
| Document processing | OpenPDF and Apache PDFBox |
| Continuous integration | GitHub Actions |
| Version control | Git and GitHub |

Technology names describe the private implementation on which this case study is based. No production dependencies or configuration files are distributed here.

## Architecture at a Glance

MerchantFlow follows a layered design with explicit responsibilities:

1. **JavaFX UI** presents workflows and collects user intent.
2. **Application services** coordinate use cases and transaction boundaries.
3. **Domain rules** protect business invariants and state transitions.
4. **Repositories** isolate persistence access.
5. **SQLite and Flyway** provide local persistence and controlled schema evolution.
6. **Adapters** integrate external marketplaces, email, printing, and file exports.

The design favors explicit contracts, separation of concerns, fail-closed decisions, and preservation of historical records.

## Selected Engineering Challenges

### Customer identity

Names are not unique, email addresses may change, and historical orders must remain attributable. Customer resolution therefore requires explicit evidence and must not silently merge ambiguous records.

### Address versioning

A customer's current address and the address recorded for an earlier order serve different purposes. The architecture separates mutable customer-master information from immutable or protected order history.

### Safe state transitions

Booking, cancellation, document creation, and synchronization are treated as controlled business operations. Invalid or ambiguous transitions are blocked rather than repaired silently.

### Incremental database evolution

Flyway migrations provide repeatable, versioned schema evolution. Migration behavior is tested so that existing installations can be upgraded without losing business data.

### Edition and license boundaries

Capabilities are evaluated centrally instead of being scattered across individual screens. Security-relevant uncertainty follows a fail-closed policy.

## Quality Strategy

The private implementation uses automated unit, integration, migration, repository, policy, and regression tests. The regression suite has executed more than 8,000 test cases during recent development phases.

Test volume alone is not treated as proof of quality. Tests are organized around contracts, boundary conditions, data preservation, failure behavior, and previously observed regressions.

## Human-Led, AI-Assisted Development

AI was used as an engineering assistant within a controlled development process. It did not own product or architectural decisions.

| Human responsibility | AI assistance |
| --- | --- |
| Requirements and priorities | Implementation proposals |
| Architecture decisions | Alternative designs |
| Domain invariants | Boilerplate and refactoring suggestions |
| Acceptance criteria | Test-case proposals |
| Review of code and tests | Diagnostic support |
| Security and privacy approval | Documentation support |
| Final acceptance | Iterative implementation assistance |

Typical workflow:

1. Define the business problem and constraints.
2. Record the architecture decision and invariants.
3. Divide the change into a small, reviewable phase.
4. Use AI to propose or implement within that boundary.
5. Review the result against the contract.
6. Execute focused tests and the full regression suite.
7. Accept, revise, or reject the change.
8. Commit only the verified result and validate it in CI.

## My Role

My responsibilities included requirements analysis, domain modelling, architecture planning, phased delivery, acceptance-criteria definition, review of AI-assisted implementation, regression control, and final technical approval.

The goal was not merely to produce features, but to establish a maintainable engineering process for evolving an internal solution into a merchant-oriented product.

## Documentation

- Product overview: [English](docs/01-product-overview.md) · [Deutsch](docs/01-product-overview.de.md)
- Technology stack: [English](docs/02-technology-stack.md) · [Deutsch](docs/02-technology-stack.de.md)
- System architecture: [English](docs/03-system-architecture.md) · [Deutsch](docs/03-system-architecture.de.md)
- Domain model: [English](docs/04-domain-model.md) · [Deutsch](docs/04-domain-model.de.md)
- Database design: [English](docs/05-database-design.md) · [Deutsch](docs/05-database-design.de.md)
- System context diagram: [English](diagrams/system-context.md) · [Deutsch](diagrams/system-context.de.md)
- Conceptual domain diagram: [English](diagrams/domain-model.md) · [Deutsch](diagrams/domain-model.de.md)
- Publication scope and safety rules: [English](PUBLICATION-SCOPE.md) · [Deutsch](PUBLICATION-SCOPE.de.md)

Additional documents will cover the order lifecycle, customer and address versioning, licensing, CI, testing, privacy, and engineering decision records. Every portfolio document will be provided in English and German.

## Portfolio Boundary

This case study intentionally demonstrates architecture, reasoning, documentation, and engineering governance rather than publishing the commercial application itself. See [Publication Scope and Safety Rules](PUBLICATION-SCOPE.md) for the binding public-content boundary.

## Copyright

Copyright © 2026 Mohamed Abergna. All rights reserved.

No open-source license is granted by this repository.

