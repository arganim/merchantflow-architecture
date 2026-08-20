# Sicherheitsgrenzen

Dieses Diagramm zeigt den wesentlichen Vertrauens- und Validierungspfad. Produktive Topologie, Zugangsdaten, Anbieter und defensive Implementierungsdetails werden bewusst ausgelassen.

```mermaid
flowchart TD
    USER["Benutzereingabe"]
    CHANNEL["Externer Kanal oder Datei"]
    UI["JavaFX-Präsentationsvalidierung"]
    ADAPTER["Connector- und Transportvalidierung"]
    APP["Vorbedingungen der Application Services"]
    DOMAIN["Domänen-Policies und Funktionsprüfungen"]
    STORE["Kontrollierte lokale Persistenz"]
    OUTPUT["Freigegebene Dokumente, Nachrichten und Exporte"]
    BLOCK["Mit sicherem Ergebnis ablehnen"]

    USER --> UI
    CHANNEL --> ADAPTER
    UI --> APP
    ADAPTER --> APP
    APP --> DOMAIN
    DOMAIN -->|akzeptiert| STORE
    DOMAIN -->|ungültig oder nicht autorisiert| BLOCK
    STORE --> OUTPUT
```

## Lesart des Diagramms

- Benutzer- und externe Eingaben bleiben bis zu den passenden Prüfungen nicht vertrauenswürdig.
- Transport-Parsing besitzt nicht die Domänenannahme.
- Anwendungs- und Domänengrenzen schützen Transaktionen und Funktionen.
- Nur akzeptierter Zustand erreicht lokale Persistenz und freigegebene Ausgaben.
- Abgelehnte Vorgänge liefern ein sicheres Ergebnis ohne Teilmutation.

## Verwandte Dokumentation

- [Sicherheit und Datenschutz](../docs/11-security-and-privacy.de.md)
- [Systemarchitektur](../docs/03-system-architecture.de.md)
- [Teststrategie](../docs/10-testing-strategy.de.md)
