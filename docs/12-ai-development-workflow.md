# AI-Assisted Development Workflow

## 1. Purpose and Scope

This document explains how AI is used as an engineering assistant in the MerchantFlow development process while requirements, architecture, risk decisions, review, and final approval remain human responsibilities.

It describes governance and quality controls rather than publishing private prompts, source code, repository content, customer data, credentials, or proprietary implementation context.

## 2. Operating Principle

AI participation does not transfer engineering accountability.

The governing principle is:

> AI may propose, transform, and analyze. A human defines the contract, controls access, verifies evidence, and accepts or rejects the result.

## 3. Responsibility Model

| Human responsibility | AI assistance |
| --- | --- |
| Product goals and priorities | Clarification questions and alternative formulations |
| Requirements and constraints | Requirement decomposition suggestions |
| Architecture decisions | Trade-off analysis and option generation |
| Domain invariants | Edge-case and decision-table proposals |
| Security and privacy boundaries | Risk prompts and review checklists |
| Acceptance criteria | Test-scenario proposals |
| Tool and data permissions | Execution within the granted boundary |
| Code and test review | Diff analysis and defect hypotheses |
| Final merge and release approval | Evidence summarization |

AI never becomes the owner of a requirement, architecture decision, security exception, or release.

## 4. Workflow Overview

The controlled workflow follows these stages:

1. Define the problem and business outcome.
2. Record constraints and publication boundaries.
3. Make or confirm the architecture decision.
4. Define acceptance and failure criteria.
5. Decompose the change into a small reviewable task.
6. Grant only the context and tools required for that task.
7. Ask AI to propose or implement within the boundary.
8. Review the complete diff and assumptions.
9. Run focused tests and required regression checks.
10. Revise, reject, or accept based on evidence.
11. Commit and validate in CI.
12. Merge or release only with explicit human approval.

The [AI Development Workflow Diagram](../diagrams/ai-workflow.md) visualizes this process.

## 5. Requirements Before Prompting

AI work begins only after the human has established enough of the contract.

The task definition includes:

- Desired outcome
- In-scope files or components
- Explicit non-goals
- Domain invariants
- Security and privacy constraints
- Acceptance criteria
- Required validation
- Permitted external actions

An underspecified task can still begin with analysis, but implementation must not invent high-impact product decisions silently.

## 6. Architecture Control

Architecture decisions are recorded before broad implementation where they affect:

- Module ownership
- Transaction boundaries
- Historical-data behavior
- External integration contracts
- License and capability enforcement
- Security and privacy controls
- Migration strategy

AI can compare alternatives, but the human selects the architecture and remains accountable for consequences.

## 7. Task Decomposition

Large changes are divided into reviewable phases.

A good task boundary has:

- One principal outcome
- Known files or modules
- Defined inputs and outputs
- Limited side effects
- Explicit tests
- A reversible review point

Small boundaries reduce context leakage, simplify review, and make regression diagnosis easier.

## 8. Context Management

AI receives only the context required to complete the task.

Context controls include:

- Exclude production credentials and personal data
- Prefer sanitized examples
- Share relevant interfaces rather than entire unrelated repositories
- Identify authoritative documents
- State known assumptions explicitly
- Refresh context when the branch or requirement changes

More context is not automatically better if it introduces private data or conflicting instructions.

## 9. Tool and Permission Boundaries

Tools are granted according to task scope.

Read-only analysis does not automatically authorize:

- File mutation
- Commit or push
- Pull-request creation
- Merge
- Deployment
- Messaging or publication

Write actions require authorization and verification of the exact target. Broad access is avoided when a narrow path is sufficient.

## 10. Data Safety

AI-assisted work must not receive or publish:

- Real customer or order data
- Production database copies
- Credentials, tokens, keys, or license material
- Private connector payloads
- Sensitive support logs
- Internal commercial rules outside the approved task

Synthetic fixtures and sanitized architecture examples are preferred.

## 11. Implementation Proposals

AI can assist with:

- Boilerplate
- Refactoring proposals
- Mapping and validation code
- Test cases
- Migration review
- Documentation
- Diagnostic hypotheses
- Alternative designs

Proposals are evaluated against the architecture contract, not accepted because they are plausible or syntactically complete.

## 12. Assumption Handling

AI must surface material assumptions.

The human review asks:

- Is the assumption supported by the requirement?
- Does it change a business invariant?
- Does it expose or discard data?
- Does it alter a public interface or schema?
- Does it need a recorded architecture decision?

Unsupported high-impact assumptions cause the task to stop or return to analysis.

## 13. Diff Review

The complete change is reviewed before acceptance.

Review covers:

- Exact files changed
- Unrelated modifications
- Boundary and dependency changes
- Error and rollback behavior
- Data migration impact
- Security and privacy implications
- Tests added or changed
- Documentation accuracy
- Generated artifacts and configuration

Small-looking diffs are not exempt from review.

## 14. Test Generation

AI may propose or generate tests, but tests are not automatically trusted.

Human review verifies that each test:

- Represents a real contract
- Can fail for the intended reason
- Does not only repeat implementation logic
- Includes meaningful failure and boundary cases
- Uses synthetic data
- Remains readable and maintainable

## 15. Independent Validation

Validation is independent of the AI proposal.

Evidence can include:

- Compiler results
- Focused unit and policy tests
- Repository and integration tests
- Migration tests
- JavaFX/headless tests
- Regression suites
- Static and architecture checks
- Rendered documentation review

AI summarizes evidence but does not convert a failed gate into approval.

## 16. Regression Control

Every accepted change is evaluated against previously protected behavior.

Regression selection is risk-based and considers:

- Domain invariants
- Historical data
- State transitions
- External identity and idempotency
- Database evolution
- License and capability behavior
- Documents and exports

## 17. Failure and Revision Loop

When a proposal or test fails:

1. Preserve the failure evidence.
2. Identify whether the problem is requirement, design, implementation, test, or environment.
3. Narrow the next change.
4. Re-run the focused failing evidence.
5. Re-run the required regression set.
6. Review the new diff again.

Blind repeated prompting without diagnosis is not a controlled workflow.

## 18. Hallucination and Uncertainty

AI can produce plausible but unsupported claims.

Controls include:

- Require references to current source or authoritative documentation
- Verify unstable technical facts with primary sources
- Mark inference explicitly
- Prefer executable evidence for behavior
- Reject invented classes, APIs, migrations, or business rules
- Re-read the actual branch state before writing

## 19. Security Review

AI-assisted changes receive explicit security review when they affect:

- Input validation
- Authentication or secrets
- License enforcement
- File-system paths
- External transport
- Personal data
- Logging and diagnostics
- Build or release permissions

Security-sensitive uncertainty follows a fail-closed decision.

## 20. Privacy Review

The review checks whether a change:

- Collects additional personal data
- Changes purpose or retention
- Expands logs or exports
- Introduces real data into tests
- Changes deletion or correction behavior
- Exposes data to a new external system

Public documentation uses sanitized examples and explicit publication boundaries.

## 21. Git Discipline

AI-assisted work follows the same Git controls as human-only work:

- Start from the intended base
- Use a focused feature branch
- Inspect staged and unstaged changes
- Commit only approved scope
- Use meaningful commit messages
- Verify the remote branch
- Open one focused pull request
- Merge only after gates and approval

Automation does not justify broad staging or unrelated cleanup.

## 22. Pull-Request Discipline

A pull request documents:

- Purpose and scope
- Key design decisions
- Validation performed
- Security and publication boundary
- Remaining manual checks

Draft state is used while content or visual verification remains incomplete.

## 23. CI as Independent Evidence

CI runs outside the AI conversation and provides reproducible evidence against the committed branch.

Required failures block acceptance. AI can help diagnose them, but cannot waive them or alter protected checks without separate review.

## 24. Documentation Control

AI can help create bilingual and structured documentation.

Documentation review verifies:

- Technical accuracy
- Alignment between languages
- Working links
- Mermaid rendering
- No private names, paths, data, or secrets
- Clear distinction between fact, design, and future plan

## 25. Human Acceptance Gate

The human accepts a change only after confirming:

- The requirement is satisfied
- The architecture remains coherent
- Domain invariants are protected
- Security and privacy boundaries are respected
- Tests provide meaningful evidence
- The diff contains only intended changes
- CI and required manual checks pass

## 26. Metrics and Learning

Useful process signals can include:

- Review findings before acceptance
- Regressions prevented by new tests
- Rework caused by unclear requirements
- Flaky-test or environment failures
- Time spent diagnosing versus implementing
- Documentation defects found during visual review

Metrics improve the process and do not rank people or replace judgment.

## 27. Deliberate Non-Goals

This workflow does not support:

- Autonomous product ownership by AI
- Unreviewed code generation
- AI approval of its own tests or changes
- Sharing production secrets or customer data
- Bypassing CI or human acceptance
- Broad external actions without authorization
- Treating prompt quality as a substitute for architecture

## 28. Public Abstraction Boundary

This document deliberately excludes:

- Private prompts and conversation logs
- Production repository content
- Credentials and tool configuration
- Customer and operational data
- Internal defect histories
- Security-sensitive agent instructions
- Commercial implementation details

## 29. Related Documentation

- [System Architecture](03-system-architecture.md)
- [CI/CD Pipeline](09-ci-cd-pipeline.md)
- [Testing Strategy](10-testing-strategy.md)
- [Security and Privacy](11-security-and-privacy.md)
- [Roadmap](13-roadmap.md)
- [AI Development Workflow Diagram](../diagrams/ai-workflow.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
