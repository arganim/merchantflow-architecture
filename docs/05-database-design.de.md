# Datenbankdesign

## 1. Zweck und Umfang

Dieses Dokument beschreibt die Datenbankprinzipien von MerchantFlow. Im Mittelpunkt stehen Verantwortlichkeit, Integrität, Schutz historischer Daten, sichere Migrationen und die Betriebsmerkmale einer lokalen Desktop-Datenbank.

Es handelt sich um eine bereinigte Architekturbeschreibung. Produktive Tabellen, Spalten, Indizes, Migrationsskripte, Datenbankpfade, Kundendaten und wirtschaftlich sensible Constraints werden nicht veröffentlicht.

## 2. Designziele

Das Persistenzmodell unterstützt:

- Verlässliche lokale Transaktionen
- Explizite referenzielle Integrität
- Stabile interne Identitäten
- Schutz historischer Geschäftsunterlagen
- Kontrollierte Schemaentwicklung mit Flyway
- Deterministische Duplikatvermeidung
- Vorhersagbare Abfragen für Desktop-Arbeitsabläufe
- Testbare Repository-Verträge
- Sicherungsbewussten Betrieb
- Klare Trennung von Persistenzmodellen und externen Payloads

## 3. Speichermodell

MerchantFlow verwendet SQLite als lokale relationale Datenbank und Flyway für die versionierte Schemaentwicklung. Spring Data JPA stellt Repository-Infrastruktur bereit, während Application Services die Transaktionsgrenzen besitzen.

Die Datenbank gehört zu einer Anwendungsinstallation. Sie wird Marketplaces, Browser-Clients und externen Reporting-Werkzeugen nicht direkt bereitgestellt.

## 4. Konzeptionelle Datenbereiche

| Datenbereich | Repräsentative Datensätze | Verantwortungsregel |
| --- | --- | --- |
| Kundenstammdaten | Kundenidentität, Kontaktdaten, Herkunft, Aktivitätsstatus | Veränderbar durch kontrollierte Kundenanwendungsfälle |
| Adresshistorie | Kundenbezogene Adressversionen und Gültigkeitsmetadaten | Frühere Versionen bleiben zuordenbar |
| Produktstammdaten | Kaufmännische, steuerliche, Herkunfts-, Gewichts- und Betriebsmerkmale | Änderungen wirken auf zukünftige Vorgänge, nicht auf akzeptierte Historie |
| Bestellungen | Kopf, Herkunft, Lebenszyklus, Summen und Transaktionskontext | Das Bestellaggregat besitzt die akzeptierte Transaktion |
| Bestellpositionen | Menge, Preis, Steuer und Produkt-Snapshot | Als Teil der Bestellung geschützt |
| Bestelladressen | Für die Bestellung verwendete Rechnungs- und Lieferdaten | Unabhängig von aktuellen Kundendaten erhalten |
| Dokumente | Typ, Geschäftsidentität, Erzeugungszustand und Zuordnung | Müssen auf eine zulässige Transaktion verweisen |
| Versandausgaben | Exportidentität, Validierungszustand und Übergabemetadaten | Doppelte Übergabe wird kontrolliert |
| Externe Referenzen | Kanalgrenze und externe Geschäftsidentität | Innerhalb der Quelle eindeutig |
| Konfiguration | Nicht geheime Betriebseinstellungen und gewählte Modi | Zugriff über kontrollierte Konfigurationsservices |
| Migrationshistorie | Ausgeführte Schemaversionen und Prüfsummen | Von Flyway verwaltet, nicht von Anwendungsfällen |

Diese Namen beschreiben Konzepte und nicht das produktive Schema.

## 5. Kennungsstrategie

Interne Kennungen sind stabil und unabhängig von veränderbaren Geschäftsdaten.

Das Design trennt:

- Interne Primäridentität
- Lesbare Geschäftsnummern
- Externe Kanalkennungen
- Natürliche Suchmerkmale

Namen, E-Mail-Adressen, Dokumentnummern und externe Bestellnummern sind keine austauschbaren Identitäten. Externe Eindeutigkeit umfasst immer die Quellgrenze.

## 6. Beziehungsdesign

Beziehungen folgen Aggregatverantwortung statt uneingeschränkter Objektnavigation.

- Ein Kunde kann mehrere Adressversionen besitzen.
- Ein Kunde kann mehreren Bestellungen zugeordnet sein.
- Eine Bestellung besitzt eine oder mehrere Positionen.
- Eine Bestellung besitzt ihre Transaktionsadressen.
- Ein Produkt kann von vielen historischen Bestellpositionen referenziert werden.
- Eine Bestellung kann mehrere kontrollierte Dokument- oder Versanddatensätze erzeugen.
- Eine Bestellung kann quellenbezogene Integrationsreferenzen tragen.

Fremdschlüsselverhalten schützt die Geschäftshistorie. Kaskaden werden nur verwendet, wenn Aggregatverantwortung und Löschsemantik eindeutig sind.

## 7. Schutz historischer Daten

Die Datenbank unterscheidet aktuelle Stammdaten von akzeptierten Transaktionsdaten.

### 7.1 Adressen

Bei Änderungen wird eine geeignete Adressversion erzeugt oder ausgewählt. Bestehende Bestellungen behalten ihren historischen Rechnungs- und Lieferkontext.

### 7.2 Produkte und Preise

Produktdatensätze unterstützen zukünftige Vorgänge. Akzeptierte Bestellpositionen bewahren die Werte, die zur Rekonstruktion von Summen und Dokumenten erforderlich sind.

### 7.3 Dokumente

Erzeugte Geschäftsdokumente bleiben der zugrunde liegenden Transaktion und dem Erzeugungsvorgang zugeordnet. Korrekturen löschen nicht die Existenz der ursprünglichen Operation.

## 8. Integritätsbedingungen

Datenbank-Constraints ergänzen die Domänenvalidierung. Sie schützen Invarianten, die unabhängig vom aufrufenden Arbeitsablauf gelten.

Konzeptionelle Beispiele sind:

- Erforderliche Beziehungen dürfen nicht null sein.
- Mengen- und Geldwerte verwenden zulässige Bereiche und Darstellungen.
- Quellenbezogene externe Identitäten dürfen nicht doppelt vorkommen.
- Rollen von Bestelladressen verwenden eine erlaubte Wertemenge.
- Lebenszyklusfelder enthalten bekannte Werte.
- Versions- und Auditmetadaten erfüllen erforderliche Konsistenzregeln.

Domänen-Policies entscheiden weiterhin die fachliche Bedeutung. Ein Datenbank-Constraint ist die letzte Sicherheitsgrenze und kein Ersatz für explizite Anwendungsergebnisse.

## 9. Indexstrategie

Indizes werden aus gemessenen Zugriffspfaden und Integritätsanforderungen abgeleitet.

Typische Kategorien sind:

- Interne und Fremdschlüsselabfragen
- Eindeutigkeit von Quelle und externer Referenz
- Merkmale der Kundensuche
- Filter nach Bestellstatus und Geschäftsdatum
- Zuordnung von Dokumenten und Versand
- Filter für operative Auswertungen

Produktive Indexnamen und exakte Query-Pläne werden nicht veröffentlicht. Indizes werden bei Änderungen an Migrationen oder Repository-Abfragen erneut geprüft.

## 10. Transaktionsgrenzen

Application Services besitzen Transaktionen für vollständige Anwendungsfälle.

Ein typischer Schreibvorgang:

1. Lädt die für die Entscheidung benötigten Nachweise.
2. Prüft fachliche und anwendungsbezogene Vorbedingungen.
3. Erstellt einen akzeptierten Änderungsplan.
4. Schreibt zusammengehörige Datensätze bei Bedarf in einer Transaktion.
5. Committed nur einen vollständigen gültigen Zustand.
6. Führt nicht transaktionale externe Nebeneffekte in bewusster Reihenfolge aus.

Repository-Methoden definieren nicht selbstständig Geschäftstransaktionen.

## 11. SQLite-Betriebsmodell

SQLite passt zur Desktop-First-Auslieferung, weil es eine eingebettete relationale Datenbank ohne separat administrierten Server bereitstellt.

Die Architektur berücksichtigt:

- Begrenzte lokale Schreibkonkurrenz
- Kurze und bewusste Transaktionen
- Explizite Behandlung belegter oder gesperrter Zustände
- Verlässliche Auswahl von Dateisystem und Speicherpfad
- Koordination von Sicherungen
- Kontrolliertes Beenden der Anwendung
- Migration vor normaler Geschäftsnutzung

Das Design setzt keine uneingeschränkten gleichzeitigen Schreibzugriffe mehrerer Anwendungsknoten voraus.

## 12. Schemaentwicklung mit Flyway

Jede produktive Schemaänderung wird durch eine versionierte Migration dargestellt. Bestehende Installationen werden vorwärts aktualisiert und nicht still neu erstellt.

Migrationsprinzipien sind:

- Nach Veröffentlichung nur ergänzte Versionshistorie
- Deterministische Migrationsreihenfolge
- Prüfbare DDL- und Datentransformationen
- Explizite Standardwerte und Backfill-Strategien
- Validierung vor normalem Anwendungsstart
- Wiederherstellungshinweise bei fehlgeschlagenen Upgrades
- Regressionstests aus repräsentativen früheren Schemaständen

Die Portfolio-Dokumentation enthält keine privaten Migrationsskripte.

## 13. Migrationstests

Migrationstests prüfen mehr als einen erfolgreichen Start.

Sie umfassen:

- Erstellung einer neuen Datenbank
- Upgrade aus unterstützten historischen Versionen
- Erhalt vorhandener Kunden- und Bestelldaten
- Korrekte Erstellung neuer Constraints und Indizes
- Sicheres Backfill erforderlicher Werte
- Wiederholten Start nach abgeschlossener Migration
- Kontrollierten Fehler bei nicht unterstützten oder inkonsistenten Zuständen

## 14. JPA-Mapping-Grenzen

Persistenz-Mappings sind Infrastrukturaufgaben. Domänenentscheidungen dürfen nicht von zufälligem Lazy Loading, automatischem Cascade-Verhalten oder Entity-Gleichheit über veränderbare Felder abhängen.

Das Design bevorzugt deshalb:

- Explizite Repository-Abfragen für Anwendungsfälle
- Kontrolliertes Laden von Aggregaten
- Stabile Identitätssemantik
- Bewusstes Cascade- und Orphan-Verhalten
- Mapping-Tests für wichtige Beziehungen
- DTO-Grenzen für Oberfläche und externe Connectoren

## 15. Löschung und Aufbewahrung

Geschäftshistorie wird nicht gelöscht, nur weil aktuelle Stammdaten nicht mehr aktiv sind.

Die Architektur unterscheidet:

- Deaktivierung wiederverwendbarer Stammdaten
- Korrektur durch einen expliziten Geschäftsvorgang
- Aufbewahrung von Transaktions- und Dokumentnachweisen
- Datenschutzbedingte Löschung oder Anonymisierung nach einer getrennten kontrollierten Policy

Keine uneingeschränkte UI-Aktion kaskadiert durch historische Bestellungen und Dokumente.

## 16. Sicherung und Wiederherstellung

Sicherung wird als Anwendungsvorgang behandelt und nicht als blindes Kopieren während eines unbekannten Schreibzustands.

Die konzeptionelle Strategie umfasst:

- Durch Konfiguration ausgewählten bekannten Datenbankort
- Koordinierte oder geprüfte Snapshot-Erstellung
- Eindeutige Sicherungsmetadaten
- Validierung der Wiederherstellung
- Diagnosen, die den aktiven Speicherort ohne unnötige Offenlegung sensibler Daten identifizieren

Exakte Pfade, Zeitpläne und betriebliche Zugangsdaten bleiben privat.

## 17. Datenschutz und Sicherheit

Die Datenbank enthält in der privaten Anwendung personenbezogene und kaufmännische Informationen. Der Zugriff ist auf die Anwendung und autorisierte lokale Betriebsabläufe begrenzt.

Öffentliche Portfolio-Inhalte enthalten nicht:

- Reale Kunden- oder Bestelldatensätze
- Datenbankkopien und Exporte
- Produktive Pfade und Dateinamen
- Zugangsdaten, Tokens und Lizenzmaterial
- Vollständige Schemas und proprietäre Constraints

Protokolle und Diagnosen vermeiden unnötige personenbezogene Daten und Secrets.

## 18. Repository- und Regressionstests

Repository-Tests prüfen Abfrageverträge, Laden von Beziehungen, Eindeutigkeitsverhalten und Transaktionsergebnisse bei Bedarf gegen eine reale Datenbank-Engine.

Die Regression konzentriert sich auf:

- Duplikatvermeidung
- Erhalt historischer Daten
- Verantwortung für Adressen und Bestellungen
- Korrektheit von Auswertungen
- Migrationskompatibilität
- Fehlerverhalten bei ungültigem Zustand

## 19. Bewusste Nicht-Ziele

Die Persistenzarchitektur stellt nicht bereit:

- Öffentliche Datenbank-API
- Direkte Bearbeitung durch externe Werkzeuge
- Regionsübergreifende oder verteilte Schreibzugriffe
- Schema-Neuerstellung als normale Upgrade-Strategie
- Verstecktes destruktives Cascade-Verhalten
- Externe Payloads als verbindlichen Domänenzustand ohne Validierung

## 20. Verwandte Dokumentation

- [Domänenmodell](04-domain-model.de.md)
- [Systemarchitektur](03-system-architecture.de.md)
- [Konzeptionelles Domänendiagramm](../diagrams/domain-model.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
