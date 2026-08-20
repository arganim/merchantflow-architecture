# CI/CD Pipeline

## 1. Purpose and Scope

This document describes the conceptual continuous-integration and delivery controls used for the private MerchantFlow implementation and the disciplined Git workflow demonstrated by this portfolio repository.

The private application workflow definitions, release credentials, signing configuration, deployment destinations, and production artifacts are not published here.

## 2. Pipeline Goals

The pipeline is designed to provide:

- Reproducible builds
- Early feedback on feature branches
- Required validation before changes reach `main`
- Separation of fast and integration-level tests
- Database-migration regression protection
- Headless JavaFX validation
- Traceable artifacts and results
- Controlled release preparation
- Minimal secret exposure
- Human approval of technical and business acceptance

## 3. Git Workflow

The workflow uses short-lived feature branches and focused pull requests.

Conceptual sequence:

1. Start from the current `main` branch.
2. Create a branch for one documented change boundary.
3. Implement or document the change in reviewable increments.
4. Run focused local validation.
5. Push the branch and open a draft pull request.
6. Review content, security boundary, and rendered diagrams.
7. Mark the pull request ready only after visual and technical acceptance.
8. Merge only after required checks and explicit approval.

## 4. Pipeline Overview

A pull-request pipeline conceptually executes:

- Source checkout
- Java and Maven environment setup
- Dependency resolution
- Compilation
- Unit tests
- Integration tests
- Repository and migration tests
- Architecture and policy checks
- Headless JavaFX validation where required
- Result and artifact publication

The [CI Pipeline Diagram](../diagrams/ci-pipeline.md) shows the quality-gate structure.

## 5. Build Reproducibility

Maven defines the private application's build lifecycle and dependency graph.

Reproducibility principles include:

- Pinned or intentionally managed dependency versions
- Known Java runtime version
- Consistent Maven goals
- Version-controlled build configuration
- Clean workspace assumptions
- No dependency on developer-specific absolute paths
- Deterministic test inputs where possible

The portfolio documents these principles without distributing the private build files.

## 6. Trigger Strategy

Pipeline triggers can distinguish:

| Trigger | Primary purpose | Expected scope |
| --- | --- | --- |
| Feature-branch push | Early developer feedback | Focused build and test validation |
| Pull request | Merge-quality gate | Full required verification |
| Main-branch update | Confirm integrated state | Rebuild and integration confidence |
| Manual release workflow | Controlled release preparation | Version, packaging, signing, and approval stages |
| Scheduled validation | Detect environmental or dependency drift | Selected regression and maintenance tasks |

The exact production schedules and release triggers remain private.

## 7. Compilation Gate

Compilation verifies that the complete source tree and generated resources remain consistent under the supported Java version.

The gate catches:

- Type and API mismatches
- Missing generated resources
- Invalid dependency changes
- Module or packaging errors
- Unsupported language or runtime assumptions

Compilation success is necessary but not sufficient for acceptance.

## 8. Unit-Test Gate

Unit tests provide fast feedback for deterministic components such as:

- Normalization
- Calculations
- Domain policies
- State-transition decisions
- Capability evaluation
- Mapping helpers
- Error classification

Failures block integration. Tests are expected to explain business contracts, not only implementation details.

## 9. Integration-Test Gate

Integration tests verify collaboration across services, repositories, transactions, and infrastructure boundaries.

Representative coverage includes:

- Spring context composition
- Application-service orchestration
- Transaction rollback
- SQLite behavior
- Repository queries and constraints
- Connector mapping with controlled fakes
- Document and export service contracts

Live production systems and credentials are not required for pull-request validation.

## 10. Migration-Test Gate

Database migration tests verify both new installations and upgrades from representative historical schema states.

The gate checks:

- Deterministic migration order
- Successful fresh database creation
- Preservation of existing business records
- Required backfills and constraints
- Repeated startup after completed migration
- Controlled failure for unsupported states

Private migrations and database snapshots are not published in this repository.

## 11. Architecture and Policy Checks

Architecture tests or static checks protect boundaries that ordinary feature tests may not detect.

Examples include:

- UI code does not own domain decisions
- Domain policies do not depend on JavaFX or connector payloads
- Protected operations use central capability evaluation
- Transaction ownership remains in application services
- Forbidden dependencies do not cross module boundaries
- Public artifacts do not contain known sensitive patterns

## 12. Headless JavaFX Validation

JavaFX tests can require a graphical environment even when no physical display is available. The CI environment can provide a controlled virtual display such as Xvfb for supported UI and integration checks.

Headless validation considers:

- Deterministic toolkit startup
- Isolation between tests
- Cleanup of windows and application threads
- Timeouts for blocked UI work
- Separation of visual rendering tests from domain tests

## 13. Test Layers

| Layer | Feedback speed | Primary purpose |
| --- | --- | --- |
| Unit | Fast | Pure logic and policy contracts |
| Repository | Fast to medium | Persistence queries and constraints |
| Integration | Medium | Service, transaction, and adapter collaboration |
| Migration | Medium | Schema evolution and data preservation |
| UI/headless | Medium to slow | Desktop interaction and JavaFX integration |
| Regression | Broad | Protection against previously observed failures |

The pipeline can parallelize independent layers while preserving a clear final gate.

## 14. Quality Gate

The merge gate combines automated and human evidence.

Automated evidence includes:

- Successful build
- Required tests passed
- Migration validation passed
- No prohibited architecture violations
- Required artifacts generated

Human evidence includes:

- Requirements and acceptance criteria reviewed
- Architecture impact understood
- Security and privacy boundary checked
- Documentation and diagrams visually inspected
- Final approval recorded

## 15. Failure Handling

A failed stage stops or blocks the dependent path and exposes actionable diagnostics.

Failure reports should identify:

- Stage and test category
- Safe error summary
- Relevant log or artifact location
- Reproduction command where appropriate
- Whether the failure is deterministic, environmental, or external

Secrets and personal data are masked or excluded from logs.

## 16. Caching

Dependency caching can reduce build time but must not become an undeclared source of correctness.

Cache keys account for:

- Operating environment
- Java version
- Dependency definitions
- Relevant build configuration

A clean build remains the reference path for diagnosing cache-related failures.

## 17. Artifacts

Pull-request artifacts can include safe engineering outputs such as:

- Test reports
- Coverage summaries where used
- Static-analysis results
- Rendered documentation previews
- Non-production diagnostic packages

Production installers, signing material, private configuration, and customer data are not attached to public workflows.

## 18. Secret Management

Secrets are supplied only to stages that require them and are never stored in source control.

Controls include:

- Least-privilege environment access
- Protected release environments
- No secret exposure to untrusted pull-request code
- Masked logs
- Rotation and revocation outside the repository
- Separate test credentials for controlled integration environments

This document does not name production providers or secret identifiers.

## 19. Dependency and Supply-Chain Controls

The pipeline can include dependency and build-integrity controls such as:

- Reviewed dependency changes
- Version and provenance checks
- Vulnerability scanning
- Restricted publication permissions
- Reproducible release inputs
- Traceable commit-to-artifact association

Findings are evaluated rather than suppressed automatically.

## 20. Release Separation

Continuous integration and release are separate responsibilities.

A successful pull-request build proves that the change satisfies required verification. It does not automatically authorize a production release.

Release preparation can require:

- Approved version
- Clean main-branch commit
- Full regression result
- Release notes
- Packaging verification
- Signing in a protected environment
- Explicit human approval

## 21. Documentation Repository Controls

This public architecture repository contains documentation rather than the production application.

Its validation priorities are:

- Correct Markdown links
- Balanced and renderable Mermaid blocks
- Bilingual structure
- No secrets or private data
- Focused file scope
- Visual review of GitHub rendering

The absence of production workflow definitions here is deliberate and does not imply that private application CI is public.

## 22. AI-Assisted Development Boundary

AI may propose implementation, tests, refactoring, or documentation, but it does not approve its own output.

The CI workflow provides independent executable evidence. Human review remains responsible for:

- Requirements
- Architecture decisions
- Security and privacy approval
- Interpretation of failures
- Acceptance of generated or modified tests
- Final merge and release decisions

## 23. Metrics and Maintenance

Useful pipeline metrics can include:

- Build duration
- Failure rate by stage
- Flaky-test recurrence
- Time to restore a green main branch
- Migration-test coverage across supported versions

Metrics support maintenance decisions and are not used as a substitute for technical judgment.

## 24. Testing the Pipeline

Pipeline changes are themselves reviewed and validated.

Safe practices include:

- Small workflow changes
- Test branches or controlled dry runs
- Minimal permissions by default
- Verification of failure paths
- Review of trigger and secret scope
- Rollback plan for release-workflow changes

## 25. Deliberate Non-Goals

The public case study does not publish:

- Production workflow files
- Signing or deployment credentials
- Private artifact destinations
- Release keys or installer configuration
- Internal repository names
- Customer environments or operational endpoints

## 26. Related Documentation

- [Technology Stack](02-technology-stack.md)
- [System Architecture](03-system-architecture.md)
- [Database Design](05-database-design.md)
- [License Architecture](08-license-architecture.md)
- [CI Pipeline Diagram](../diagrams/ci-pipeline.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
