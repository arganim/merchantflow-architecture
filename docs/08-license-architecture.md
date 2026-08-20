# License Architecture

## 1. Purpose and Scope

This document describes the conceptual license and capability architecture of MerchantFlow. It explains how edition, entitlement, operational mode, and runtime evidence are translated into consistent application decisions.

The public model intentionally excludes signing keys, cryptographic implementation details, anti-tampering mechanisms, production license formats, validation endpoints, and operational bypass procedures.

## 2. Architecture Goals

The license architecture is designed to provide:

- Central capability decisions
- Consistent behavior across UI and application services
- Fail-closed handling of invalid or uncertain protected state
- Clear separation of licensing and business rules
- Support for desktop and connection-tolerant operation
- Testable decisions without real license material
- Understandable user-facing outcomes
- Minimal exposure of license and device information
- Controlled transition between operational modes

## 3. Core Concepts

| Concept | Responsibility | Important boundary |
| --- | --- | --- |
| Edition | Describes a product-level capability set | Edition names do not authorize operations by themselves |
| Entitlement | Represents an approved right or subscription scope | It is accepted only after evidence validation |
| Capability | Names one protected application operation or feature group | UI and services use the same capability identity |
| License evidence | Provides signed or otherwise trusted entitlement input | Raw evidence is not exposed across the application |
| Operational mode | Describes normal, limited, evaluation, expired, or unavailable behavior | Mode transitions follow explicit policy |
| Capability decision | Returns allow or deny with a safe reason category | Uncertainty does not become permission |
| License context | Supplies bounded runtime facts needed for evaluation | Sensitive machine and account data are minimized |

The exact production names and values remain private.

## 4. Central Evaluation Boundary

License decisions are made through one capability-evaluation boundary. Screens, controllers, application services, and adapters do not interpret raw license files independently.

The evaluator conceptually receives:

- Requested capability
- Validated entitlement information
- Current operational mode
- Bounded runtime context
- Relevant policy version

It returns a structured decision that can be logged, tested, and presented safely.

## 5. Capability-Based Design

Application code asks whether a capability is available rather than comparing edition names throughout the codebase.

This design provides:

- One place to map editions to capabilities
- Smaller impact when packaging changes
- Clear service-level authorization
- Easier policy tests
- Consistent UI visibility and messaging

An edition is a configuration of capabilities. It is not a collection of scattered `if` statements.

## 6. Layered Enforcement

Capability enforcement occurs at more than one layer.

### 6.1 Presentation layer

The UI uses the decision to:

- Show or hide optional navigation
- Enable or disable controls
- Explain why an operation is unavailable
- Avoid starting an impossible workflow

UI state improves usability but is not the authoritative protection.

### 6.2 Application-service layer

The application service checks the capability again before performing a protected use case.

This prevents:

- Bypass through another screen
- Invocation through background or integration code
- Stale UI state from authorizing an operation
- Direct calls that skip presentation controls

### 6.3 Infrastructure boundary

Adapters receive only approved commands. They do not decide product entitlement and cannot upgrade a denied capability.

## 7. Evaluation Flow

A conceptual capability check follows these stages:

1. Identify the requested capability.
2. Load bounded license state through a trusted provider.
3. Validate the evidence and its required context.
4. Determine the current operational mode.
5. Resolve entitlement into capabilities.
6. Apply policy and return a structured allow or deny decision.
7. Enforce the same decision at UI and service boundaries.

The [License Evaluation Diagram](../diagrams/license-evaluation.md) visualizes this flow.

## 8. Fail-Closed Policy

Protected operations are denied when the evaluator cannot establish a valid allow decision.

Fail-closed conditions can include:

- Missing required evidence
- Invalid or unreadable evidence
- Unsupported policy or format
- Expired entitlement
- Conflicting runtime context
- Verification failure
- Internal evaluator error

The user receives a safe reason and recovery direction where appropriate. Internal security details are not exposed in error messages.

## 9. Operational Modes

The public model uses conceptual modes:

- Normal operation
- Limited or read-only operation
- Evaluation operation
- Expired or renewal-required operation
- Unavailable because state cannot be verified

The exact production modes and transitions are private. A mode defines the policy context; individual operations still ask for explicit capabilities.

## 10. Desktop and Offline Considerations

MerchantFlow is desktop-first and may need to remain usable during temporary network unavailability.

The architecture separates:

- Local evidence validation
- Optional remote refresh or synchronization
- Time-bounded previously validated state
- Operations that can remain available offline
- Operations that require current confirmation

Offline behavior is policy-driven. Network failure does not automatically grant access, and temporary unavailability does not corrupt existing business records.

## 11. Time and Clock Handling

Time-dependent entitlement requires deliberate handling because local clocks can be wrong or changed.

The architecture treats time as an explicit input and tests:

- Boundary dates
- Expiry transitions
- Previously validated state
- Clearly inconsistent time observations
- Recovery after a valid refresh

This document does not publish clock-tampering detection or grace-period values.

## 12. Caching

Capability results can be cached only within a defined validity boundary.

Cache design considers:

- Policy and entitlement version
- Operational-mode changes
- Refresh events
- Application restart
- Expiry or validation boundaries
- Explicit invalidation after license changes

Stale cached permission must not outlive the state on which it was based.

## 13. License-State Ownership

A dedicated provider owns loading and interpreting persisted license state. Other modules receive validated views or capability decisions.

This limits:

- Exposure of raw license material
- Duplicate parsing logic
- Inconsistent error handling
- Accidental persistence of derived security state
- Dependency of domain objects on storage formats

## 14. Separation from Business Rules

Licensing decides whether a use case is available. Domain rules decide whether the requested business operation is valid.

Both conditions must succeed.

For example, an available document capability does not make an invalid order eligible for a document. Conversely, a valid order does not bypass a missing capability.

## 15. Structured Decisions

The evaluator returns a decision with bounded information such as:

- Allowed or denied
- Requested capability
- Safe reason category
- Current public operational mode
- Whether user action may restore availability

The decision does not expose keys, signatures, machine fingerprints, or internal verification traces.

## 16. Error and User Experience

License-related outcomes must be understandable without revealing security internals.

User-facing categories can distinguish:

- Feature not included
- License requires renewal
- State cannot currently be verified
- Configuration or installation requires support
- Operation is unavailable in the current mode

Technical logs use correlation and safe categories rather than complete license payloads.

## 17. Updates and Renewal

License refresh is a controlled application operation.

Conceptually it:

1. Obtains new evidence through an approved path.
2. Validates it before replacing accepted state.
3. Persists the new state atomically where appropriate.
4. Invalidates derived capability caches.
5. Re-evaluates the operational mode.
6. Reports a structured result.

A failed refresh does not silently replace known valid evidence with unusable data.

## 18. Revocation and Downgrade

When entitlement becomes unavailable, the architecture protects existing business data.

Policy can restrict new protected operations while still permitting appropriate access to existing records. Downgrade does not delete documents, orders, or customer history.

The exact commercial downgrade behavior is outside this public case study.

## 19. Security Boundary

Security-sensitive components are isolated behind narrow interfaces.

Public documentation does not expose:

- Signing or verification keys
- Cryptographic algorithms and parameter choices
- Machine-binding inputs
- Internal integrity checks
- License-file structure
- Remote validation routes
- Debug or support overrides

The architecture communicates responsibilities without providing an implementation blueprint for bypass.

## 20. Privacy

License evaluation minimizes collection and propagation of account, device, and environment information.

Diagnostics avoid unnecessary identifiers. Public examples use synthetic values and do not contain real license records or customer information.

## 21. Testing Strategy

Representative tests cover:

- Capability included and excluded by entitlement
- UI and service enforcement consistency
- Missing, invalid, expired, and unsupported evidence
- Operational-mode transitions
- Offline and refresh behavior
- Cache invalidation
- Time-boundary conditions
- Failure without partial protected operation
- Preservation of existing business data after downgrade

Tests use controlled fakes and synthetic evidence, never production keys.

## 22. Deliberate Non-Goals

This public architecture does not define:

- A complete digital-rights-management system
- Protection based only on hidden UI controls
- License checks distributed through arbitrary business code
- Automatic permission when validation is uncertain
- Deletion of business data after entitlement changes
- Public support bypasses or recovery secrets

## 23. Related Documentation

- [System Architecture](03-system-architecture.md)
- [Domain Model](04-domain-model.md)
- [CI/CD Pipeline](09-ci-cd-pipeline.md)
- [License Evaluation Diagram](../diagrams/license-evaluation.md)
- [Publication Scope](../PUBLICATION-SCOPE.md)
