# Technologiestack

## 1. Zweck

Dieses Dokument erklärt die wichtigsten Technologien hinter der privaten Implementierung, die durch die MerchantFlow-Architektur-Fallstudie dargestellt wird. Im Mittelpunkt stehen technische Begründungen und Abwägungen. Produktive Konfigurationen, Dependency-Dateien und interner Quellcode werden nicht veröffentlicht.

MerchantFlow ist eine Desktop-First-Anwendung. Spring Boot wird innerhalb des Desktopprozesses als Application Framework und Dependency-Injection-Container eingesetzt. Das Produkt wird weder als Browseranwendung noch als verteiltes Microservice-System dargestellt.

## 2. Entscheidungsfaktoren

Die Technologieauswahl wurde durch folgende Anforderungen bestimmt:

- Zuverlässiger lokaler Betrieb geschäftskritischer Prozesse
- Starke Typisierung und langfristige Wartbarkeit
- Direkter Desktopzugriff auf Druck und kontrollierte Dateierstellung
- Transaktionale lokale Datenhaltung
- Versionierte Datenbankentwicklung für installierte Anwendungen
- Testbare Trennung von Oberfläche, Anwendungsfällen, Domänenregeln und Infrastruktur
- Integration von Marketplaces, E-Mail, Dokumenten und Versandexporten
- Reproduzierbare Builds und automatisierte Regressionsprüfung
- Kontrollierte Weiterentwicklung von einer internen Lösung zu einem Händlerprodukt

## 3. Überblick

| Aufgabe | Technologie | Hauptverantwortung |
| --- | --- | --- |
| Programmiersprache | Java 21 | Typsichere Implementierung von Anwendung und Domäne |
| Desktop-Oberfläche | JavaFX | Native Arbeitsabläufe, Formulare, Tabellen, Dialoge und Druckintegration |
| Application Framework | Spring Boot 3.2.5 | Dependency Injection, Konfiguration, Service-Komposition und Lebenszyklus |
| Persistenzabbildung | Spring Data JPA / Hibernate | Repository-Abstraktion und Entity-Persistenz |
| Lokale Datenbank | SQLite | Transaktionale Speicherung lokaler Geschäftsdaten |
| Schemaentwicklung | Flyway | Geordnete, versionierte und testbare Datenbankmigrationen |
| Buildsystem | Maven | Reproduzierbare Builds, Abhängigkeiten, Profile und Testausführung |
| Automatisierte Tests | JUnit 5 und Spring-Testunterstützung | Unit-, Integrations-, Migrations-, Policy- und Regressionstests |
| PDF-Erstellung | OpenPDF | Erzeugung von Geschäftsdokumenten |
| PDF-Verarbeitung | Apache PDFBox | Prüfung, Druck und Verarbeitung von PDF-Dokumenten |
| Tabellenverarbeitung | Apache POI | Strukturierte Excel-Exporte und Berichte |
| HTTP-Integration | Apache HttpClient 5 | Kontrollierte Kommunikation mit externen Connectoren |
| Versionsverwaltung | Git und GitHub | Nachvollziehbare Änderungen, Branches, Commits und Zusammenarbeit |
| Continuous Integration | GitHub Actions mit Xvfb | Headless-Kompilierung und automatisierte Java-/JavaFX-Testprüfung |

Die Versionen und Bibliotheksnamen beschreiben die private Implementierung zum Zeitpunkt dieser Fallstudie. Sie sind dokumentierte Fakten und keine veröffentlichten Abhängigkeiten.

## 4. Java 21

Java 21 bildet die Sprach- und Laufzeitgrundlage. Der Long-Term-Support-Zeitraum eignet sich für eine Geschäftsanwendung, die über mehrere Produktphasen wartbar bleiben muss.

Die wichtigsten Vorteile für diese Architektur sind:

- Statische Typisierung für Domänenmodelle und Serviceverträge
- Ausgereifte Werkzeuge und Bibliotheken
- Klare Behandlung von Ausnahmen und Transaktionen
- Gute Unterstützung automatisierter Tests
- Kompatibilität mit Spring Boot und JavaFX
- Vorhersehbares Deployment- und Laufzeitverhalten

Domänenregeln werden durch explizite Typen, Policies und Statusübergänge ausgedrückt und nicht in lose strukturierten Daten versteckt.

## 5. JavaFX

JavaFX ermöglicht eine Desktop-First-Oberfläche mit nativen Fenstern, Dialogen, Formularen, Tabellen, Validierungsmeldungen sowie Integration von lokalem Druck und Dateiprozessen.

Diese Wahl passt zu einer Anwendung, die häufig folgende Aufgaben koordiniert:

- Lokale Dokumente und Exportordner
- Drucker und Druckdialoge
- Länger laufende Datenerfassungsprozesse
- Offline- oder verbindungstoleranter Betrieb
- Direkter Zugriff auf lokal gespeicherte Geschäftsunterlagen

JavaFX bleibt an der Präsentationsgrenze. UI-Komponenten nehmen Benutzerabsichten entgegen, stellen Anwendungszustände dar und delegieren Geschäftsvorgänge an Application Services. Sie sind nicht die verbindliche Quelle für Domänenregeln.

## 6. Spring Boot innerhalb einer Desktopanwendung

Spring Boot übernimmt die Zusammensetzung der Anwendung, ohne ihren Desktopcharakter zu verändern. Der JavaFX-Lebenszyklus und der Spring Application Context laufen innerhalb eines gemeinsam auslieferbaren Anwendungsprozesses.

Spring wird hauptsächlich eingesetzt für:

- Dependency Injection
- Service-Komposition
- Repository-Integration
- Konfigurationsgrenzen
- Transaktionsverwaltung
- Testkontexte
- Austauschbare Adapter

Dadurch wird manuelle Objektverdrahtung vermieden, während klare Service- und Repository-Verträge erhalten bleiben. Der Einsatz von Spring Boot bedeutet nicht, dass die Anwendung einen Browser oder einen entfernten Server benötigt.

## 7. SQLite und Spring Data JPA

SQLite bietet eingebettete transaktionale Speicherung mit geringem Betriebsaufwand. Das passt zu einem lokal installierten Desktopprodukt, bei dem ein eigener Datenbankserver zusätzliche Deployment- und Administrationskomplexität verursachen würde.

Spring Data JPA stellt Repository-Abstraktionen bereit und verbindet die Persistenz mit den Transaktionsgrenzen der Application Services.

Die Abwägungen werden bewusst festgehalten:

- SQLite passt zum vorgesehenen lokalen Betriebsmodell, nicht zu unbegrenzter verteilter Parallelität.
- Datenbankpfad und Laufzeitmodus müssen eindeutig aufgelöst werden.
- Sicherung, Migration und Diagnosen sind Bestandteil der Produktarchitektur.
- Schreibvorgänge benötigen kontrollierte Transaktionsgrenzen.
- Historische Geschäftsbeziehungen müssen Änderungen an Stammdaten überstehen.

Die Datenbank wird externen Anwendungen nicht direkt zur Verfügung gestellt. Integrationen kommunizieren über kontrollierte Anwendungsgrenzen.

## 8. Flyway

Flyway behandelt Datenbankentwicklung als geordneten Engineering-Prozess. Jede Schemaänderung erhält eine versionierte Migration und wird sowohl gegen neue als auch gegen bestehende Datenbankstände geprüft.

Die Migrationsstrategie soll Folgendes gewährleisten:

- Reproduzierbare Schemaerstellung
- Sichere Aktualisierung bestehender Installationen
- Explizite Reihenfolge und Historie
- Testbare Backfills und Constraints
- Schutz vor manueller Schemaabweichung
- Regressionsschutz für bereits veröffentlichte Strukturen

Produktive Migrationsskripte und Datenbankdefinitionen werden bewusst nicht in diesem öffentlichen Repository veröffentlicht.

## 9. Dokumente, Druck und Tabellenexporte

OpenPDF wird zur Erstellung von Geschäftsdokumenten verwendet. Apache PDFBox unterstützt Prüfung und Druck von PDF-Dateien. Apache POI ermöglicht strukturierte Tabellenexporte.

Diese Bibliotheken liegen hinter Application Services, damit:

- Domänenentscheidungen vor der Darstellung stattfinden
- erzeugte Dokumente geprüfte Bestelldaten verwenden
- Dateinamen und Speicherregeln zentral bleiben
- Drucken nicht zu einem reinen UI-Nebeneffekt wird
- dokumentbezogene Fehler einheitlich behandelt werden können

Historische Dokumente werden als Geschäftsunterlagen und nicht als beliebige UI-Ausgabe behandelt.

## 10. Externe Integrationen

Apache HttpClient 5 unterstützt HTTP-basierte Adapter wie Marketplace-Connectoren. Externe Payloads werden zunächst in interne Anwendungsverträge überführt, bevor sie das Domänenmodell beeinflussen dürfen.

Die Integrationsregel lautet:

1. Externe Daten über einen Connector-Adapter abrufen.
2. Transportspezifische Felder in eine interne Darstellung überführen.
3. Daten normalisieren und validieren.
4. Kunden-, Adress-, Produkt- und Bestellentscheidungen über Domänen-Policies auflösen.
5. Nur ein akzeptiertes lokales Ergebnis speichern.

Externe Systeme sind Datenquellen oder Auslieferungsziele. Sie dürfen lokale fachliche Invarianten nicht umgehen.

## 11. Tests und Continuous Integration

JUnit 5 und die Spring-Testunterstützung ermöglichen mehrere Teststufen:

- Unit-Tests für Normalisierung, Berechnung und Policy-Verhalten
- Repository-Tests für Persistenzverträge
- Integrationstests für das Zusammenspiel von Services und Datenbank
- Migrationstests für Schemaänderungen und Backfills
- Regressionstests für bereits beobachtete Fehler
- Architekturorientierte Prüfungen für kritische Grenzen

GitHub Actions validiert das Projekt in einer kontrollierten Java-21-Umgebung. Xvfb stellt auf Linux-CI-Runnern ein virtuelles Display für JavaFX-bezogene Tests bereit.

CI ist eine Validierungsstufe und kein Ersatz für die lokale Prüfung. Eine Änderung wird erst akzeptiert, nachdem fokussierte Tests, Regressionsauswirkungen, Datensicherheit und Architekturvertrag bewertet wurden.

## 12. Architekturstil und Abwägungen

MerchantFlow ist bewusst als modularer Monolith innerhalb eines Desktopprozesses entworfen.

Diese Entscheidung bietet:

- Einfaches Deployment
- Lokale transaktionale Konsistenz
- Direkte Desktopintegration
- Geringere Betriebskomplexität
- Klare Modulgrenzen ohne verteilte Systemkomplexität

Auf Microservices wird verzichtet, weil die aktuellen Produktanforderungen weder Netzwerkverteilung noch Service Discovery, verteilte Transaktionen oder getrennten Servicebetrieb rechtfertigen.

Modularität bleibt trotzdem wichtig. Kunden, Bestellungen, Produkte, Dokumente, Versand, Buchhaltung, Integrationen, Lizenzierung und Konfiguration werden durch Anwendungs- und Domänenverträge getrennt, obwohl sie im selben Prozess laufen.

## 13. Verwaltung von Abhängigkeiten

Abhängigkeiten werden nur aufgenommen, wenn sie eine klar definierte Fähigkeit bereitstellen, die nicht sinnvoll individuell implementiert werden sollte. Die Auswahl berücksichtigt:

- Kompatibilität mit Java 21
- Wartungsstatus
- Eignung der Lizenz
- Sicherheitshistorie
- Testbarkeit
- Laufzeitumfang
- Möglichkeit zur Kapselung hinter einer Anwendungsgrenze

Upgrades gelten als technische Änderungen und müssen denselben Review- und Regressionsprozess wie neue Funktionen durchlaufen.

## 14. Öffentliche Portfolio-Grenze

Dieses Dokument erklärt Technologieentscheidungen, ohne Folgendes zu veröffentlichen:

- Produktive Builddateien
- Informationen zur exakten Dependency-Auflösung
- Zugangsdaten und Secrets
- Umgebungskonfiguration
- Interne Dateipfade
- Datenbankmigrationen
- Connector-Endpunkte
- Private CI-Workflow-Dateien oder Logs

Die verbindlichen Veröffentlichungsregeln stehen im Publication-Scope-Dokument des Repositorys.
