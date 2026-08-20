# Security and Privacy

## 1. Purpose and Scope

This document describes the conceptual security and privacy controls of MerchantFlow and the publication safeguards used for this architecture portfolio.

It is an engineering overview, not a certification, legal opinion, penetration-test report, or claim of compliance with every jurisdiction. Production security configuration, credentials, keys, endpoints, customer data, and detailed defensive mechanisms remain private.

## 2. Security Goals

The architecture is intended to provide:

- Clear trust boundaries
- Least-privilege access
- Validation of untrusted input
- Protection of local business records
- Central enforcement of protected capabilities
- Controlled documents, exports, and external communication
- Secret isolation
- Safe diagnostics and logging
- Backup-aware data protection
- Privacy by data minimization and purpose limitation
- Publication controls for the public portfolio

## 3. Protected Assets

Representative protected assets include:

- Customer identity and contact data
- Billing and shipping addresses
- Orders, prices, tax context, and documents
- Marketplace and email credentials
- Local database and backups
- License evidence and security-sensitive configuration
- Generated PDFs, spreadsheets, and shipping files
- Build, release, and signing material

The public repository contains none of these production assets.

## 4. Data Classification

| Classification | Examples | Handling principle |
| --- | --- | --- |
| Public | Sanitized architecture documentation | Reviewed for safe publication |
| Internal | Non-secret engineering details and diagnostics | Limited to authorized development use |
| Confidential | Customer, order, partner, and business records | Processed only for approved application purposes |
| Secret | Credentials, tokens, signing keys, and protected license material | Isolated, minimized, and never committed |

Classification informs storage, logging, testing, export, and publication decisions.

## 5. Trust Boundaries

MerchantFlow treats these inputs as untrusted until validated:

- User-entered form data
- Marketplace and connector payloads
- Imported files
- Configuration values
- License evidence
- File-system paths and selected output locations
- Data restored from backup or upgraded through migration

The [Security Boundaries Diagram](../diagrams/security-boundaries.md) shows the principal validation path.

## 6. Input Validation

Validation occurs at the appropriate layer:

- Presentation formatting and required-field feedback in JavaFX
- Application preconditions in use-case services
- Business invariants in domain policies
- Technical constraints in repositories and the database
- Transport validation in connectors and adapters

UI validation alone is never treated as a security boundary.

## 7. External Connectors

Connectors isolate authentication, transport, parsing, and technical error translation.

Security controls include:

- Treat payloads as untrusted
- Parse into bounded transport models
- Normalize before business comparison
- Preserve source identity
- Reject unsupported or malformed input
- Avoid logging complete payloads
- Apply timeouts and bounded retry
- Prevent transport code from mutating domain state directly

## 8. Authentication and Secrets

Credentials and tokens are stored outside source control and exposed only to components that require them.

Principles include:

- No secrets in repository files
- No secrets in public examples
- Least-privilege access
- Masked logs
- Rotation and revocation through approved operational processes
- Separate test credentials
- No secret transmission through application diagnostics

The exact credential store and provider configuration remain private.

## 9. Local Desktop Security

MerchantFlow is a local desktop application. Its security model includes both application controls and the host operating environment.

The application controls:

- Approved storage-path selection
- Database access through repositories
- Capability enforcement
- Validation and transaction boundaries
- Controlled export and diagnostics

The operating environment remains responsible for:

- User-account access
- Disk and device protection
- File-system permissions
- Endpoint security
- Physical access
- Secure backup destination

## 10. Local Database Protection

The SQLite database is not exposed as a public API.

Controls include:

- Access through application repositories
- Referential integrity and constraints
- Controlled migrations
- Short transactions
- Backup coordination
- Diagnostics that avoid revealing unnecessary paths
- No direct connector or browser access

Exact paths, file names, schemas, and encryption decisions are not published.

## 11. Transaction Integrity

Security also includes protection against inconsistent business state.

Application services:

- Load required evidence
- Validate before mutation
- Apply related writes atomically where appropriate
- Roll back failed local operations
- Separate database success from external side-effect success
- Return explicit failure results

## 12. Capability Enforcement

Protected operations use the central license and capability evaluator.

Enforcement occurs in both:

- UI presentation for clear user experience
- Application services for authoritative protection

Invalid or uncertain protected state follows a fail-closed policy. Detailed license-security controls remain private.

## 13. Documents and Exports

Documents and exports can contain personal or commercially sensitive data.

Controls include:

- Eligibility checks before generation
- Approved output fields
- Deterministic file naming and destination policy
- Duplicate-output protection
- Clear attribution to the source order
- Error handling without false success
- No publication of real output samples

Users remain responsible for securing exported files after delivery to their chosen destination.

## 14. Email and Messaging

Email delivery is an external side effect.

The application verifies:

- Approved business purpose
- Intended recipient data
- Required document eligibility
- Configured transport boundary
- Delivery result separate from transaction result

Secrets and complete message content are excluded from diagnostics.

## 15. Logging and Diagnostics

Logs are designed to be useful without becoming a secondary data store.

Logging principles include:

- Safe event and reason categories
- Correlation identifiers where useful
- No passwords, tokens, signing material, or full license evidence
- No complete customer addresses or external payloads unless strictly required and protected
- Bounded retention
- Clear distinction between user-facing messages and technical diagnostics

## 16. Error Handling

Technical exceptions are translated into structured application failures.

User-facing output should:

- Explain the safe next action
- Avoid stack traces and infrastructure details
- Avoid revealing whether sensitive identifiers exist
- Distinguish validation, authorization, persistence, and transport failures

## 17. Backups and Recovery

Backups contain the same sensitive business data as the active database.

The backup strategy considers:

- Coordinated snapshot creation
- Approved destination
- Access control
- Integrity and recovery validation
- Retention policy
- Secure disposal
- Clear identification without excessive path disclosure

Public documentation does not contain backup locations, schedules, or credentials.

## 18. Migration Security

Database migrations execute with authority to change the local schema and data.

Controls include:

- Version-controlled private migration scripts
- Review before release
- Deterministic ordering
- Test upgrades from supported states
- Failure before normal business use when migration is invalid
- No dynamic migration content from untrusted sources

## 19. Dependency and Build Security

Build and dependency controls can include:

- Reviewed dependency changes
- Known Java and Maven environment
- Vulnerability and provenance checks
- Minimal CI permissions
- Protected release and signing stages
- Traceable commit-to-artifact association

Findings are assessed according to reachability and risk rather than ignored or treated as automatic proof of compromise.

## 20. Privacy Principles

MerchantFlow applies privacy-oriented engineering principles:

- Data minimization
- Purpose limitation
- Accuracy through controlled correction
- Storage limitation through explicit retention policy
- Integrity and confidentiality
- Traceability of important operations
- Separation of test and production data

Specific legal obligations depend on deployment context and require qualified review.

## 21. Data Retention and Deletion

Customer master data, transaction history, and legally relevant documents can have different retention requirements.

The architecture distinguishes:

- Deactivation for future use
- Correction of inaccurate data
- Retention of transaction evidence
- Privacy-driven deletion or anonymization
- Secure disposal of backups and exports

Deletion is a controlled policy, not an unrestricted cascade from a UI button.

## 22. Data-Subject Requests

Where applicable, operational processes can support access, correction, export, restriction, or deletion requests.

Implementation must account for:

- Identity verification
- Legal retention duties
- Scope across active data, documents, backups, and exports
- Auditability of the response
- Avoidance of disclosure to the wrong person

This portfolio does not claim a universal legal workflow.

## 23. Security Testing

Representative security tests cover:

- Invalid and malformed external input
- Path and file-boundary validation
- Capability enforcement
- Fail-closed license behavior
- Secret and personal-data log exclusions
- Transaction rollback
- Migration failure
- Duplicate and replay behavior
- Public-repository sensitive-pattern checks

Testing complements design review and operational controls.

## 24. Incident and Recovery Readiness

Operational readiness includes the ability to:

- Identify affected component and data class
- Stop unsafe external communication
- Preserve relevant evidence safely
- Rotate or revoke exposed credentials
- Restore from a validated backup
- Communicate and document corrective action

Production incident contacts and procedures remain private.

## 25. Public Repository Safety

This repository is governed by a publication boundary.

Before publication, content is checked for:

- Customer, order, partner, and employee information
- Credentials, tokens, keys, and license material
- Production domains, endpoints, and paths
- Database files, exports, and screenshots
- Private repository and package names
- Commercially sensitive decision rules
- Metadata that can reveal operational details

## 26. Deliberate Omissions

This public document does not reveal:

- Credentials or key-management design
- Exact encryption and signing configuration
- Anti-tampering implementation
- Internal threat-intelligence or detection rules
- Production network and storage topology
- Penetration-test findings
- Customer-specific controls or legal advice

## 27. Related Documentation

- [System Architecture](03-system-architecture.md)
- [Database Design](05-database-design.md)
- [License Architecture](08-license-architecture.md)
- [CI/CD Pipeline](09-ci-cd-pipeline.md)
- [Testing Strategy](10-testing-strategy.md)
- [Security Boundaries Diagram](../diagrams/security-boundaries.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
