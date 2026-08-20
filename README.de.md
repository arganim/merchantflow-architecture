# MerchantFlow — Architektur-Fallstudie

> Eine bereinigte Architektur-Fallstudie zu einer Desktop-Handelsplattform auf Basis von Java 21.

[English version](README.md)

## Über dieses Repository

MerchantFlow dokumentiert die Architektur und die Engineering-Arbeitsweise hinter einer privaten, produktionsorientierten Handelsplattform. Die Fallstudie wurde eigenständig für Bewerbungen und als professionelles Portfolio erstellt.

Der produktive Quellcode, Kundendaten, Zugangsdaten, Datenbankdateien und wirtschaftlich sensible Regeln sind bewusst ausgeschlossen. Dieses Repository enthält ausschließlich Dokumentation und Diagramme; es ist keine ausführbare Anwendung.

## Das fachliche Problem

Kleine und mittlere Händler verwalten Bestellungen, Kundendaten, Versand, Buchhaltungsdokumente und Marketplace-Daten häufig in voneinander getrennten Werkzeugen. Dadurch entstehen doppelte Arbeit, inkonsistente Daten, geringe Nachvollziehbarkeit und vermeidbare operative Risiken.

MerchantFlow beschreibt eine Desktop-First-Plattform, die diese Abläufe in einer kontrollierten Anwendung zusammenführt. Historische Geschäftsunterlagen bleiben geschützt, während externe Verkaufskanäle schrittweise angebunden werden können.

## Zentrale Funktionsbereiche

- Kunden- und Adressverwaltung
- Produkt- und bestandsbezogene Stammdaten
- Manuelle Bestellungen und Marketplace-Bestellungen
- Erstellung von Rechnungen, Lieferscheinen und Stornodokumenten
- Vorbereitung und Export von Versanddaten
- Buchhaltungsorientierte Auswertungen
- Anbindung externer Verkaufskanäle
- Datenbankmigrationen und sicherungsbewusster Betrieb
- Editions- und lizenzabhängige Funktionssteuerung
- Diagnosen, Validierungen und nachvollziehbare Arbeitsabläufe

## Technologiestack

| Bereich | Technologie |
| --- | --- |
| Programmiersprache | Java 21 |
| Desktop-Oberfläche | JavaFX |
| Application Framework | Spring Boot |
| Build und Abhängigkeiten | Maven |
| Persistenz | SQLite und Spring Data JPA |
| Datenbankmigrationen | Flyway |
| Tests | JUnit 5 und Integrationstests |
| Dokumentverarbeitung | OpenPDF und Apache PDFBox |
| Continuous Integration | GitHub Actions |
| Versionsverwaltung | Git und GitHub |

Die Technologien beschreiben die private Implementierung, auf deren Entwicklungserfahrung diese Fallstudie basiert. Produktive Abhängigkeiten und Konfigurationen werden nicht veröffentlicht.

## Architektur im Überblick

MerchantFlow folgt einer Schichtenarchitektur mit klaren Verantwortlichkeiten:

1. Die **JavaFX-Oberfläche** stellt Arbeitsabläufe dar und nimmt Benutzerabsichten entgegen.
2. **Application Services** koordinieren Anwendungsfälle und Transaktionsgrenzen.
3. **Domänenregeln** schützen fachliche Invarianten und Statusübergänge.
4. **Repositories** kapseln den Persistenzzugriff.
5. **SQLite und Flyway** ermöglichen lokale Datenhaltung und kontrollierte Schemaänderungen.
6. **Adapter** verbinden Marketplaces, E-Mail, Druck und Dateiexporte.

Die Architektur bevorzugt explizite Verträge, Trennung der Verantwortlichkeiten, Fail-closed-Entscheidungen und den Schutz historischer Geschäftsunterlagen.

## Ausgewählte Engineering-Herausforderungen

### Kundenidentität

Namen sind nicht eindeutig, E-Mail-Adressen können sich ändern und historische Bestellungen müssen weiterhin korrekt zugeordnet bleiben. Kunden dürfen deshalb nicht allein anhand eines unsicheren Merkmals still zusammengeführt werden.

### Adressversionierung

Die aktuelle Adresse eines Kunden und die in einer früheren Bestellung verwendete Adresse erfüllen unterschiedliche Zwecke. Die Architektur trennt veränderbare Kundenstammdaten von unveränderlichen oder besonders geschützten Bestelldaten.

### Sichere Statusübergänge

Buchung, Stornierung, Dokumenterstellung und Synchronisierung werden als kontrollierte Geschäftsvorgänge behandelt. Ungültige oder mehrdeutige Übergänge werden blockiert und nicht stillschweigend korrigiert.

### Schrittweise Datenbankentwicklung

Flyway-Migrationen ermöglichen nachvollziehbare und versionierte Schemaänderungen. Migrationen werden getestet, damit bestehende Installationen ohne Verlust von Geschäftsdaten aktualisiert werden können.

### Editions- und Lizenzgrenzen

Funktionen werden zentral bewertet und nicht auf einzelne Oberflächen verteilt. Bei sicherheitsrelevanter Unsicherheit gilt das Fail-closed-Prinzip.

## Qualitätsstrategie

Die private Implementierung verwendet automatisierte Unit-, Integrations-, Migrations-, Repository-, Policy- und Regressionstests. Die Regressionssuite hat in den letzten Entwicklungsphasen mehr als 8.000 Testfälle ausgeführt.

Die reine Anzahl der Tests gilt nicht als Qualitätsnachweis. Entscheidend sind geprüfte Verträge, Grenzfälle, Datenerhalt, Fehlerverhalten und der Schutz vor bereits beobachteten Regressionen.

## Vom Menschen gesteuerte, KI-unterstützte Entwicklung

KI wurde als Engineering-Assistent innerhalb eines kontrollierten Entwicklungsprozesses eingesetzt. Produkt- und Architekturentscheidungen blieben in menschlicher Verantwortung.

| Verantwortung von Mohamed | Unterstützung durch KI |
| --- | --- |
| Anforderungen und Prioritäten | Implementierungsvorschläge |
| Architekturentscheidungen | Lösungsalternativen |
| Fachliche Invarianten | Boilerplate und Refactoring-Vorschläge |
| Akzeptanzkriterien | Vorschläge für Testfälle |
| Prüfung von Code und Tests | Unterstützung bei Diagnosen |
| Sicherheits- und Datenschutzfreigabe | Unterstützung bei Dokumentation |
| Finale Abnahme | Iterative Implementierungsunterstützung |

Typischer Ablauf:

1. Geschäftsproblem und Grenzen definieren.
2. Architekturentscheidung und Invarianten festhalten.
3. Änderung in eine kleine, prüfbare Phase aufteilen.
4. KI innerhalb dieser Grenze zur Unterstützung einsetzen.
5. Ergebnis gegen den vereinbarten Vertrag prüfen.
6. Fokussierte Tests und vollständige Regression ausführen.
7. Änderung akzeptieren, überarbeiten oder ablehnen.
8. Nur das geprüfte Ergebnis committen und durch CI validieren.

## Meine Rolle

Meine Verantwortung umfasste Anforderungsanalyse, Domänenmodellierung, Architekturplanung, phasenweise Umsetzung, Definition von Akzeptanzkriterien, Prüfung der KI-unterstützten Implementierung, Regressionskontrolle und finale technische Freigabe.

Das Ziel bestand nicht nur darin, Funktionen umzusetzen. Es sollte ein wartbarer Engineering-Prozess entstehen, mit dem sich eine interne Lösung schrittweise zu einem Produkt für Händler weiterentwickeln lässt.

## Dokumentation

- Produktübersicht: [Deutsch](docs/01-product-overview.de.md) · [English](docs/01-product-overview.md)
- Technologiestack: [Deutsch](docs/02-technology-stack.de.md) · [English](docs/02-technology-stack.md)
- Systemarchitektur: [Deutsch](docs/03-system-architecture.de.md) · [English](docs/03-system-architecture.md)
- Domänenmodell: [Deutsch](docs/04-domain-model.de.md) · [English](docs/04-domain-model.md)
- Datenbankdesign: [Deutsch](docs/05-database-design.de.md) · [English](docs/05-database-design.md)
- Bestellablauf: [Deutsch](docs/06-order-workflow.de.md) · [English](docs/06-order-workflow.md)
- Kunden- und Adressversionierung: [Deutsch](docs/07-customer-address-versioning.de.md) · [English](docs/07-customer-address-versioning.md)
- Systemkontextdiagramm: [Deutsch](diagrams/system-context.de.md) · [English](diagrams/system-context.md)
- Konzeptionelles Domänendiagramm: [Deutsch](diagrams/domain-model.de.md) · [English](diagrams/domain-model.md)
- Bestellablaufdiagramm: [Deutsch](diagrams/order-flow.de.md) · [English](diagrams/order-flow.md)
- Diagramm zur Kundenzuordnung: [Deutsch](diagrams/customer-flow.de.md) · [English](diagrams/customer-flow.md)
- Veröffentlichungsgrenzen und Sicherheitsregeln: [Deutsch](PUBLICATION-SCOPE.de.md) · [English](PUBLICATION-SCOPE.md)

Weitere Dokumente werden Lizenzierung, CI, Tests, Datenschutz und Architecture Decision Records behandeln. Jedes Portfolio-Dokument wird vollständig auf Deutsch und Englisch bereitgestellt.

## Abgrenzung des Portfolios

Diese Fallstudie zeigt bewusst Architektur, Entscheidungsfindung, Dokumentation und Engineering-Governance, ohne die kommerzielle Anwendung selbst zu veröffentlichen. Die verbindlichen Grenzen stehen in den [Veröffentlichungsgrenzen und Sicherheitsregeln](PUBLICATION-SCOPE.de.md).

## Urheberrecht

Copyright © 2026 Mohamed Abergna. Alle Rechte vorbehalten.

Durch dieses Repository wird keine Open-Source-Lizenz eingeräumt.


