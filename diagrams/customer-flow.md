# Customer and Address Resolution Flow

This diagram shows how customer evidence and address versions are resolved before an order receives its historical address context.

```mermaid
flowchart TD
    INPUT["Customer and address input"]
    NORMALIZE["Normalize comparison values"]
    EVIDENCE{"Identity evidence"}
    EXISTING["Reuse verified customer"]
    CREATE["Create new customer"]
    BLOCK["Require review or block"]
    ADDRESS{"Equivalent address version?"}
    REUSE["Reuse address version"]
    VERSION["Create new address version"]
    SNAPSHOT["Preserve order address context"]

    INPUT --> NORMALIZE
    NORMALIZE --> EVIDENCE
    EVIDENCE -->|verified| EXISTING
    EVIDENCE -->|no candidate| CREATE
    EVIDENCE -->|ambiguous or conflicting| BLOCK
    EXISTING --> ADDRESS
    CREATE --> ADDRESS
    ADDRESS -->|yes| REUSE
    ADDRESS -->|no, valid change| VERSION
    ADDRESS -->|invalid or ambiguous| BLOCK
    REUSE --> SNAPSHOT
    VERSION --> SNAPSHOT
```

## Reading the Diagram

- Normalization supports comparison but does not decide identity.
- A verified customer can be reused; no candidate can lead to controlled creation.
- Ambiguous evidence does not modify an existing customer.
- Equivalent addresses can reuse a version; valid changes create a new version.
- The order preserves its own billing and shipping context after resolution.

## Related Documentation

- [Customer and Address Versioning](../docs/07-customer-address-versioning.md)
- [Order Workflow](../docs/06-order-workflow.md)
- [Conceptual Domain Model](domain-model.md)
