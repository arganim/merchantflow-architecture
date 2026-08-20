# CI Pipeline

This diagram shows the conceptual pull-request quality gate for MerchantFlow. Production workflow definitions, credentials, and artifact destinations remain private.

```mermaid
flowchart TD
    DEV["Developer change"]
    BRANCH["Feature branch"]
    PR["Draft pull request"]
    BUILD["Compile"]
    UNIT["Unit and policy tests"]
    INTEGRATION["Integration and repository tests"]
    MIGRATION["Migration tests"]
    ARCH["Architecture and security checks"]
    GATE{"All required checks pass?"}
    REVIEW["Human review and visual acceptance"]
    MAIN["Merge to main"]
    BLOCK["Block and diagnose"]

    DEV --> BRANCH
    BRANCH --> PR
    PR --> BUILD
    BUILD --> UNIT
    BUILD --> INTEGRATION
    BUILD --> MIGRATION
    BUILD --> ARCH
    UNIT --> GATE
    INTEGRATION --> GATE
    MIGRATION --> GATE
    ARCH --> GATE
    GATE -->|yes| REVIEW
    GATE -->|no| BLOCK
    REVIEW --> MAIN
    BLOCK --> BRANCH
```

## Reading the Diagram

- A pull request remains reviewable while independent quality stages execute.
- Compile, unit, integration, migration, and architecture evidence converge on one gate.
- Failed required checks block integration and return diagnostics to the branch.
- Automated success does not replace human architecture, privacy, and visual review.
- Release authorization is a separate process after integration.

## Related Documentation

- [CI/CD Pipeline](../docs/09-ci-cd-pipeline.md)
- [Technology Stack](../docs/02-technology-stack.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
