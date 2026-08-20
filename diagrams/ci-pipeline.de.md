# CI-Pipeline

Dieses Diagramm zeigt die konzeptionelle Pull-Request-Qualitätsgrenze von MerchantFlow. Produktive Workflow-Definitionen, Zugangsdaten und Artefaktziele bleiben privat.

```mermaid
flowchart TD
    DEV["Entwickleränderung"]
    BRANCH["Feature-Branch"]
    PR["Draft-Pull-Request"]
    BUILD["Kompilierung"]
    UNIT["Unit- und Policy-Tests"]
    INTEGRATION["Integrations- und Repository-Tests"]
    MIGRATION["Migrationstests"]
    ARCH["Architektur- und Sicherheitsprüfungen"]
    GATE{"Alle erforderlichen Checks bestanden?"}
    REVIEW["Menschliche Prüfung und visuelle Abnahme"]
    MAIN["Merge nach main"]
    BLOCK["Blockieren und diagnostizieren"]

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
    GATE -->|ja| REVIEW
    GATE -->|nein| BLOCK
    REVIEW --> MAIN
    BLOCK --> BRANCH
```

## Lesart des Diagramms

- Ein Pull Request bleibt prüfbar, während unabhängige Qualitätsstufen ausgeführt werden.
- Kompilierungs-, Unit-, Integrations-, Migrations- und Architekturnachweise laufen in einer Qualitätsgrenze zusammen.
- Fehlgeschlagene erforderliche Checks blockieren die Integration und liefern Diagnosen an den Branch zurück.
- Automatisierter Erfolg ersetzt keine menschliche Architektur-, Datenschutz- und visuelle Prüfung.
- Release-Autorisierung ist ein getrennter Prozess nach der Integration.

## Verwandte Dokumentation

- [CI/CD-Pipeline](../docs/09-ci-cd-pipeline.de.md)
- [Technologiestack](../docs/02-technology-stack.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
