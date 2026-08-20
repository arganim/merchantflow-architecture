# AI-Assisted Development Workflow

This diagram shows the controlled human-led workflow used for AI-assisted engineering.

```mermaid
flowchart TD
    REQUIREMENTS["Human requirements and constraints"]
    ARCH["Architecture decision"]
    ACCEPTANCE["Acceptance and failure criteria"]
    TASK["Small task and permission boundary"]
    AI["AI proposal or implementation"]
    REVIEW{"Human diff and assumption review"}
    TESTS["Focused tests and regression"]
    CI{"CI and manual gates pass?"}
    MERGE["Human approval and merge"]
    REVISE["Diagnose, narrow and revise"]

    REQUIREMENTS --> ARCH
    ARCH --> ACCEPTANCE
    ACCEPTANCE --> TASK
    TASK --> AI
    AI --> REVIEW
    REVIEW -->|accepted for validation| TESTS
    REVIEW -->|incorrect or unsafe| REVISE
    TESTS --> CI
    CI -->|yes| MERGE
    CI -->|no| REVISE
    REVISE --> TASK
```

## Reading the Diagram

- Human requirements and architecture precede AI implementation.
- AI receives a bounded task and permission scope.
- The complete diff and assumptions are reviewed before validation.
- Tests and CI provide independent evidence.
- Failure returns to diagnosis and a narrower task.
- Final approval and merge remain human actions.

## Related Documentation

- [AI-Assisted Development Workflow](../docs/12-ai-development-workflow.md)
- [CI/CD Pipeline](../docs/09-ci-cd-pipeline.md)
- [Testing Strategy](../docs/10-testing-strategy.md)
- [Roadmap](../docs/13-roadmap.md)
