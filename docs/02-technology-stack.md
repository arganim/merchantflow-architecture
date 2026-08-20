# Technology Stack

## 1. Purpose

This document explains the main technologies behind the private implementation represented by the MerchantFlow architecture case study. It focuses on engineering rationale and trade-offs rather than publishing production configuration, dependency files, or internal source code.

MerchantFlow is a desktop-first application. Spring Boot is used as an application framework and dependency-injection container inside the desktop process; the product is not presented as a browser application or a distributed microservice system.

## 2. Decision Drivers

The technology choices were guided by the following requirements:

- Reliable local operation for business-critical workflows
- Strong typing and long-term maintainability
- Direct desktop access to printing and controlled file generation
- Transactional local persistence
- Versioned database evolution for installed applications
- Testable separation between UI, use cases, domain rules, and infrastructure
- Integration with marketplaces, email, documents, and shipping exports
- Repeatable builds and automated regression validation
- A controlled path from an internal solution to a merchant-oriented product

## 3. Stack Overview

| Concern | Technology | Primary responsibility |
| --- | --- | --- |
| Programming language | Java 21 | Type-safe application and domain implementation |
| Desktop user interface | JavaFX | Native desktop workflows, forms, tables, dialogs, and printing integration |
| Application framework | Spring Boot 3.2.5 | Dependency injection, configuration, service composition, and lifecycle management |
| Persistence mapping | Spring Data JPA / Hibernate | Repository abstraction and entity persistence |
| Local database | SQLite | Transactional local business-data storage |
| Schema evolution | Flyway | Ordered, versioned, and testable database migrations |
| Build system | Maven | Reproducible builds, dependency management, profiles, and test execution |
| Automated testing | JUnit 5 and Spring test support | Unit, integration, migration, policy, and regression testing |
| PDF generation | OpenPDF | Generation of business documents |
| PDF processing | Apache PDFBox | Inspection, printing, and processing of PDF documents |
| Spreadsheet processing | Apache POI | Structured Excel exports and reports |
| HTTP integration | Apache HttpClient 5 | Controlled communication with external connectors |
| Version control | Git and GitHub | Traceable changes, branches, commits, and repository collaboration |
| Continuous integration | GitHub Actions with Xvfb | Headless compilation and automated Java/JavaFX test validation |

The versions and library names reflect the private implementation at the time this case study was prepared. They are documentation facts, not distributed dependencies.

## 4. Java 21

Java 21 provides the language and runtime foundation. Its long-term-support lifecycle is suitable for a business application that must remain maintainable across multiple product phases.

The main benefits for this architecture are:

- Static typing for domain models and service contracts
- Mature tooling and library ecosystem
- Clear exception and transaction handling
- Strong support for automated testing
- Compatibility with Spring Boot and JavaFX
- Predictable deployment and runtime behavior

Domain rules are expressed through explicit types, policies, and state transitions instead of relying on loosely structured data.

## 5. JavaFX

JavaFX supports a desktop-first user experience with native windows, dialogs, forms, tables, validation feedback, and integration with local printing and file workflows.

This choice is appropriate because the application coordinates operational work that often involves:

- Local documents and export directories
- Printers and print dialogs
- Long-lived data-entry workflows
- Offline or connectivity-tolerant operation
- Immediate access to locally stored business records

JavaFX is kept at the presentation boundary. UI components should collect user intent, display application state, and delegate business operations to application services. They should not become the authoritative location for domain rules.

## 6. Spring Boot Inside a Desktop Application

Spring Boot provides application composition without changing the desktop nature of the product. The JavaFX lifecycle and the Spring application context are integrated within one deployable application process.

Spring is primarily used for:

- Dependency injection
- Service composition
- Repository integration
- Configuration boundaries
- Transaction management
- Test application contexts
- Replaceable adapters

This avoids manual object wiring while preserving clear service and repository contracts. It does not imply that the application requires a web browser or a remote server.

## 7. SQLite and Spring Data JPA

SQLite provides embedded transactional storage with minimal operational overhead. It fits a locally installed desktop product where a dedicated database server would add unnecessary deployment and administration complexity.

Spring Data JPA provides repository abstractions and integrates persistence with application-service transaction boundaries.

Important trade-offs are explicit:

- SQLite is suitable for the intended local operating model, not for unrestricted multi-node concurrency.
- Database-file location and runtime mode must be resolved deterministically.
- Backup, migration, and diagnostics are part of the product architecture.
- Write operations require controlled transaction boundaries.
- Historical business relationships must survive master-data changes.

The architecture does not expose the database directly to external applications. Integrations communicate through controlled application boundaries.

## 8. Flyway

Flyway treats database evolution as an ordered engineering process. Each schema change receives a versioned migration and is validated against both new and existing database states.

The migration strategy aims to provide:

- Reproducible schema creation
- Safe upgrades of existing installations
- Explicit ordering and history
- Testable backfills and constraints
- Protection against manual schema drift
- Regression coverage for previously released structures

Production migration scripts and database definitions are intentionally excluded from this public repository.

## 9. Documents, Printing, and Spreadsheet Exports

OpenPDF is used to generate business documents. Apache PDFBox supports PDF inspection and printing workflows. Apache POI supports structured spreadsheet exports.

These libraries are placed behind application services so that:

- Domain decisions occur before rendering
- Generated documents use validated order data
- File naming and storage rules remain centralized
- Printing does not become a UI-only side effect
- Document-related failures can be handled consistently

Historical documents are treated as business records, not as disposable UI output.

## 10. External Integrations

Apache HttpClient 5 supports HTTP-based adapters such as marketplace connectors. External payloads are mapped into local application contracts before they can influence the domain model.

The integration policy is:

1. Retrieve external data through a connector adapter.
2. Map transport-specific fields into an internal representation.
3. Normalize and validate the data.
4. Resolve customer, address, product, and order decisions through domain policies.
5. Persist only an accepted local result.

External systems are data sources or delivery targets; they do not bypass local business invariants.

## 11. Testing and Continuous Integration

JUnit 5 and Spring test support provide several test levels:

- Unit tests for normalization, calculation, and policy behavior
- Repository tests for persistence contracts
- Integration tests for service and database interaction
- Migration tests for schema evolution and backfills
- Regression tests for previously observed failures
- Architecture-oriented checks for critical boundaries

GitHub Actions validates the project in a controlled Java 21 environment. Xvfb provides a virtual display for JavaFX-related tests on Linux CI runners.

CI is a validation gate, not a substitute for local review. A change is accepted only after its focused tests, regression impact, data-safety implications, and architecture contract have been reviewed.

## 12. Architecture Style and Trade-offs

MerchantFlow is intentionally designed as a modular monolith within one desktop process.

This choice provides:

- Simple deployment
- Local transactional consistency
- Direct desktop integration
- Lower operational complexity
- Clear module boundaries without distributed-system overhead

The architecture avoids microservices because the current product requirements do not justify network distribution, service discovery, distributed transactions, or independent service operations.

Modularity remains important. Customer, order, product, document, shipping, accounting, integration, licensing, and configuration concerns are separated through application and domain contracts even though they run in one process.

## 13. Dependency Governance

Dependencies are introduced only when they provide a clear capability that should not be implemented ad hoc. Selection considers:

- Compatibility with Java 21
- Maintenance status
- License suitability
- Security history
- Testability
- Runtime footprint
- Ability to isolate the dependency behind an application boundary

Upgrades are treated as engineering changes and must pass the same review and regression process as feature development.

## 14. Public Portfolio Boundary

This document explains technology choices without publishing:

- Production build files
- Dependency-lock information
- Runtime credentials
- Environment configuration
- Internal file-system paths
- Database migrations
- Connector endpoints
- Private CI workflow files or logs

The binding disclosure rules are defined in the repository's publication-scope document.
