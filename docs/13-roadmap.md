# Roadmap

## 1. Purpose and Scope

This roadmap describes the evolution of the MerchantFlow architecture portfolio and the decision themes for future product engineering.

It is not a delivery promise, sales commitment, release calendar, or disclosure of the private product backlog. Dates, commercial priorities, customer requests, and security-sensitive work remain outside the public repository.

## 2. Roadmap Principles

The roadmap follows these principles:

- Architecture decisions precede broad implementation
- Historical-data safety remains a release condition
- Quality gates grow with product risk
- External integrations remain behind controlled adapters
- Security and privacy are continuous work
- Roadmap items require evidence before commitment
- Public documentation stays sanitized and current

## 3. Portfolio Delivery Status

| Phase | Scope | Status |
| --- | --- | --- |
| Foundation | Product overview, publication boundary, bilingual README | Complete |
| Technology and architecture | Stack rationale, system architecture, context | Complete |
| Domain and persistence | Domain model, database design, conceptual relationships | Complete |
| Business workflows | Order workflow, customer identity, address versioning | Complete |
| Governance | License architecture and CI/CD controls | Complete |
| Quality and protection | Testing strategy, security, and privacy | Complete |
| Engineering process | AI-assisted development workflow and roadmap | Complete with this phase |

The portfolio can continue to evolve, but the initial architecture case-study set is complete.

## 4. Completed Documentation Set

The completed set covers:

- Product problem and scope
- Technology decisions
- Layered and modular architecture
- Domain concepts and persistence principles
- Order, customer, and address workflows
- License and capability governance
- CI/CD and testing strategy
- Security and privacy boundaries
- Human-led, AI-assisted engineering
- Future decision themes

All primary portfolio documents are available in English and German.

## 5. Product Evolution Themes

Future product work can be organized around themes rather than premature feature promises.

Potential themes include:

- Deeper operational workflow coverage
- Additional external-channel adapters
- Improved diagnostics and supportability
- Packaging and update reliability
- Reporting and export maturity
- Accessibility and internationalization
- Security and privacy hardening
- Performance and data-volume validation
- Extension points with controlled contracts

Each theme requires a separate decision and acceptance boundary.

## 6. Decision Gate for New Work

Before a roadmap item becomes committed work, it is evaluated for:

- User and business value
- Domain-model impact
- Data migration requirements
- Security and privacy impact
- License and edition impact
- Integration and operational complexity
- Testing and support cost
- Rollback or recovery strategy

## 7. Workflow Maturity

Future workflow improvements can focus on:

- Clearer exception handling
- Better review queues for ambiguous imports
- Stronger idempotency and replay visibility
- Improved document and shipping traceability
- Consistent bulk-operation safety
- Reduced manual repetition without hiding decisions

Automation remains bounded by explicit business rules.

## 8. Integration Maturity

New connectors should reuse the established adapter boundary.

An integration is ready only when it has:

- Defined source identity
- Mapping and normalization contract
- Duplicate and retry behavior
- Error classification
- Safe credential handling
- Synthetic contract fixtures
- Observability without personal-data leakage
- Clear ownership and support path

## 9. Data and Migration Maturity

Database evolution continues through versioned migrations.

Future work can include:

- Broader upgrade-path fixtures
- Performance verification for larger data sets
- Improved recovery diagnostics
- Retention and anonymization workflows
- Safer import and export validation
- Clear compatibility policy for supported versions

No roadmap item bypasses historical-data preservation.

## 10. Quality Maturity

Quality improvements can include:

- Risk-based regression selection
- Better flaky-test diagnosis
- Additional architecture checks
- Contract testing for new connectors
- Performance and resource-usage baselines
- More deterministic JavaFX testing
- Release evidence summaries

Metrics remain supporting evidence rather than automatic approval.

## 11. Security Maturity

Security work is continuous and may include:

- Periodic threat-model review
- Dependency and supply-chain improvements
- Secret-rotation exercises
- Backup and recovery testing
- Reduced diagnostic data exposure
- File and path boundary review
- Capability-enforcement regression
- Incident-readiness exercises

Sensitive implementation details remain private.

## 12. Privacy Maturity

Privacy-oriented evolution can include:

- Data inventory review
- Purpose and retention documentation
- Controlled correction and deletion workflows
- Export minimization
- Better privacy-safe diagnostics
- Review of new external data transfers
- Synthetic test-data maintenance

Legal interpretation requires qualified review for the actual deployment context.

## 13. Desktop Operations

Desktop-product maturity can include:

- Installer reliability
- Safe update and rollback behavior
- Storage-location diagnostics
- Backup guidance
- Offline and degraded-mode clarity
- Environment compatibility testing
- Support bundles that exclude secrets and unnecessary personal data

## 14. Performance and Scale

Performance work is driven by measured workflows.

Potential evidence includes:

- Startup time
- Large-order and reporting behavior
- Import throughput
- Database query plans
- Memory use during document generation
- UI responsiveness during background work

The architecture avoids scale claims without representative measurements.

## 15. Accessibility and Internationalization

Future UI work can improve:

- Keyboard navigation
- Focus behavior
- Clear validation messages
- Text scaling and layout resilience
- Translation completeness
- Locale-aware dates, numbers, and currencies
- Separation of user language from business data

## 16. Observability and Supportability

Operational support can mature through:

- Health and configuration summaries
- Migration-state visibility
- Safe correlation identifiers
- Structured error categories
- Actionable recovery guidance
- Privacy-reviewed support exports

Diagnostics must remain useful without becoming a data leak.

## 17. Packaging and Release

Release maturity can include:

- Reproducible packaging
- Protected signing stages
- Installer validation
- Version and compatibility metadata
- Release notes tied to accepted changes
- Recovery guidance
- Separation of CI success and release authorization

## 18. Architecture Decision Records

Future high-impact decisions should be captured as concise Architecture Decision Records.

Candidates include decisions about:

- New integration patterns
- Storage or deployment changes
- Update strategy
- Public extension contracts
- Retention behavior
- Major dependency changes

An ADR records context, decision, alternatives, and consequences without exposing sensitive operational details.

## 19. Portfolio Maintenance

The public repository requires ongoing maintenance:

- Keep bilingual documents aligned
- Verify links after structural changes
- Recheck Mermaid rendering
- Remove obsolete claims
- Update roadmap status deliberately
- Repeat publication-safety review
- Avoid adding production examples for convenience

## 20. Publication Readiness

Before the repository becomes public, the final gate checks:

- Documentation set is complete
- README navigation is accurate
- Mermaid diagrams render correctly
- No secrets, personal data, or production identifiers are present
- Copyright and no-license notice remain clear
- Repository description and visibility are intentional
- The public window and later privacy plan are understood

## 21. Future Public Changes

Future public updates should use the same workflow:

1. Create a focused branch.
2. Add sanitized bilingual content.
3. Check links, formatting, and publication boundary.
4. Open a draft pull request.
5. Perform visual review.
6. Merge only after explicit acceptance.

## 22. Deliberate Non-Commitments

This roadmap does not promise:

- Delivery dates
- Specific customers or partners
- Named marketplace integrations
- Pricing or edition changes
- Public release of source code
- A particular hosting or deployment model
- Compliance certification

## 23. Completion Criteria

The initial portfolio is complete when:

- Documents 01 through 13 exist in both languages
- Core diagrams are linked and renderable
- Publication scope is enforced
- Main branch contains the reviewed content
- No open content pull request remains
- Final repository audit passes
- Visibility change is made only by deliberate final approval

## 24. Related Documentation

- [Product Overview](01-product-overview.md)
- [System Architecture](03-system-architecture.md)
- [CI/CD Pipeline](09-ci-cd-pipeline.md)
- [Testing Strategy](10-testing-strategy.md)
- [Security and Privacy](11-security-and-privacy.md)
- [AI-Assisted Development Workflow](12-ai-development-workflow.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
