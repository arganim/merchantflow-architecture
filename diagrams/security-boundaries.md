# Security Boundaries

This diagram shows the principal trust and validation path. It intentionally omits production topology, credentials, providers, and defensive implementation details.

```mermaid
flowchart TD
    USER["User input"]
    CHANNEL["External channel or file"]
    UI["JavaFX presentation validation"]
    ADAPTER["Connector and transport validation"]
    APP["Application-service preconditions"]
    DOMAIN["Domain policies and capability checks"]
    STORE["Controlled local persistence"]
    OUTPUT["Approved documents, messages and exports"]
    BLOCK["Reject with safe result"]

    USER --> UI
    CHANNEL --> ADAPTER
    UI --> APP
    ADAPTER --> APP
    APP --> DOMAIN
    DOMAIN -->|accepted| STORE
    DOMAIN -->|invalid or unauthorized| BLOCK
    STORE --> OUTPUT
```

## Reading the Diagram

- User and external input remain untrusted until the appropriate checks pass.
- Transport parsing does not own domain acceptance.
- Application and domain boundaries protect transactions and capabilities.
- Only accepted state reaches local persistence and approved output.
- Rejected operations return a safe result without partial mutation.

## Related Documentation

- [Security and Privacy](../docs/11-security-and-privacy.md)
- [System Architecture](../docs/03-system-architecture.md)
- [Testing Strategy](../docs/10-testing-strategy.md)
