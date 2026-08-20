# Lizenzbewertung

Dieses Diagramm zeigt die öffentliche Funktionsbewertungsgrenze. Lizenzformate, kryptografische Details, Schlüssel, Gerätebindung und Umgehungsschutz werden bewusst ausgelassen.

```mermaid
flowchart TD
    REQUEST["Angeforderte Funktion"]
    CONTEXT["Begrenzter Lizenzkontext"]
    VERIFY{"Nachweis gültig und unterstützt?"}
    ENTITLEMENT["Berechtigung und Modus auflösen"]
    DECISION{"Funktion erlaubt?"}
    UI["Verfügbarkeit und Grund darstellen"]
    SERVICE["Im Application Service durchsetzen"]
    DENY["Sicher verweigern"]
    EXECUTE["Geschützten Anwendungsfall ausführen"]

    REQUEST --> CONTEXT
    CONTEXT --> VERIFY
    VERIFY -->|ja| ENTITLEMENT
    VERIFY -->|nein oder unsicher| DENY
    ENTITLEMENT --> DECISION
    DECISION -->|ja| UI
    DECISION -->|ja| SERVICE
    DECISION -->|nein| DENY
    SERVICE --> EXECUTE
    DENY --> UI
```

## Lesart des Diagramms

- Rohe Lizenznachweise werden nur hinter der zentralen Grenze interpretiert.
- Ungültiger oder unsicherer geschützter Zustand führt zur Verweigerung.
- UI-Verfügbarkeit und Service-Autorisierung verwenden dieselbe Funktionsentscheidung.
- Ein sichtbares oder aktiviertes Control ersetzt keine Service-Durchsetzung.
- Nach der Lizenzautorisierung gilt weiterhin die Domänenvalidierung.

## Verwandte Dokumentation

- [Lizenzarchitektur](../docs/08-license-architecture.de.md)
- [Systemarchitektur](../docs/03-system-architecture.de.md)
- [CI/CD-Pipeline](../docs/09-ci-cd-pipeline.de.md)
