# CI/CD-Pipeline

## 1. Zweck und Umfang

Dieses Dokument beschreibt die konzeptionellen Continuous-Integration- und Delivery-Kontrollen der privaten MerchantFlow-Implementierung sowie den disziplinierten Git-Ablauf dieses Portfolio-Repositorys.

Private Workflow-Definitionen, Release-Zugangsdaten, Signaturkonfigurationen, Deployment-Ziele und produktive Artefakte werden hier nicht veröffentlicht.

## 2. Pipeline-Ziele

Die Pipeline unterstützt:

- Reproduzierbare Builds
- Frühes Feedback auf Feature-Branches
- Erforderliche Validierung vor Änderungen an `main`
- Trennung schneller und integrationsbezogener Tests
- Regressionsschutz für Datenbankmigrationen
- Headless-Validierung von JavaFX
- Nachvollziehbare Artefakte und Ergebnisse
- Kontrollierte Release-Vorbereitung
- Minimale Offenlegung von Secrets
- Menschliche Freigabe technischer und fachlicher Akzeptanz

## 3. Git-Ablauf

Der Ablauf verwendet kurzlebige Feature-Branches und fokussierte Pull Requests.

Konzeptionelle Reihenfolge:

1. Vom aktuellen `main`-Branch starten.
2. Branch für eine abgegrenzte Änderung erstellen.
3. Änderung in prüfbaren Schritten implementieren oder dokumentieren.
4. Fokussierte lokale Validierung ausführen.
5. Branch veröffentlichen und Draft-Pull-Request öffnen.
6. Inhalt, Sicherheitsgrenze und gerenderte Diagramme prüfen.
7. Pull Request erst nach visueller und technischer Abnahme freigeben.
8. Erst nach erforderlichen Checks und expliziter Zustimmung mergen.

## 4. Pipeline-Überblick

Eine Pull-Request-Pipeline führt konzeptionell aus:

- Source Checkout
- Einrichtung von Java- und Maven-Umgebung
- Auflösung von Abhängigkeiten
- Kompilierung
- Unit-Tests
- Integrationstests
- Repository- und Migrationstests
- Architektur- und Policy-Prüfungen
- Headless-JavaFX-Validierung, soweit erforderlich
- Veröffentlichung von Ergebnissen und Artefakten

Das [CI-Pipeline-Diagramm](../diagrams/ci-pipeline.de.md) zeigt die Qualitätsstufen.

## 5. Reproduzierbarkeit des Builds

Maven definiert Build-Lebenszyklus und Abhängigkeitsgraph der privaten Anwendung.

Prinzipien der Reproduzierbarkeit sind:

- Festgelegte oder bewusst verwaltete Abhängigkeitsversionen
- Bekannte Java-Laufzeitversion
- Konsistente Maven-Ziele
- Versionsverwaltete Build-Konfiguration
- Annahme eines sauberen Arbeitsverzeichnisses
- Keine Abhängigkeit von entwicklerspezifischen absoluten Pfaden
- Soweit möglich deterministische Testeingaben

Das Portfolio dokumentiert diese Prinzipien, ohne private Build-Dateien zu verteilen.

## 6. Trigger-Strategie

Pipeline-Trigger können unterscheiden:

| Trigger | Hauptzweck | Erwarteter Umfang |
| --- | --- | --- |
| Feature-Branch-Push | Frühes Entwicklerfeedback | Fokussierte Build- und Testvalidierung |
| Pull Request | Merge-Qualitätsgrenze | Vollständige erforderliche Verifikation |
| Aktualisierung von `main` | Integrierten Zustand bestätigen | Erneuter Build und Integrationssicherheit |
| Manueller Release-Workflow | Kontrollierte Release-Vorbereitung | Version, Packaging, Signatur und Freigaben |
| Geplante Validierung | Umgebungs- oder Abhängigkeitsdrift erkennen | Ausgewählte Regression und Wartung |

Die genauen produktiven Zeitpläne und Release-Trigger bleiben privat.

## 7. Kompilierungsstufe

Die Kompilierung prüft, dass der vollständige Quellbaum und erzeugte Ressourcen unter der unterstützten Java-Version konsistent bleiben.

Die Stufe erkennt:

- Typ- und API-Konflikte
- Fehlende erzeugte Ressourcen
- Ungültige Abhängigkeitsänderungen
- Modul- oder Packaging-Fehler
- Nicht unterstützte Sprach- oder Laufzeitannahmen

Erfolgreiche Kompilierung ist notwendig, aber nicht ausreichend für die Abnahme.

## 8. Unit-Test-Stufe

Unit-Tests liefern schnelles Feedback für deterministische Komponenten wie:

- Normalisierung
- Berechnungen
- Domänen-Policies
- Entscheidungen über Statusübergänge
- Funktionsbewertung
- Mapping-Hilfen
- Fehlerklassifikation

Fehler blockieren die Integration. Tests sollen Geschäftsverträge und nicht nur Implementierungsdetails erklären.

## 9. Integrationstest-Stufe

Integrationstests prüfen die Zusammenarbeit von Services, Repositories, Transaktionen und Infrastrukturgrenzen.

Repräsentative Abdeckung umfasst:

- Zusammensetzung des Spring Context
- Orchestrierung durch Application Services
- Transaktionsrollback
- SQLite-Verhalten
- Repository-Abfragen und Constraints
- Connector-Mapping mit kontrollierten Fakes
- Verträge von Dokument- und Exportservices

Live-Produktivsysteme und Zugangsdaten sind für Pull-Request-Validierung nicht erforderlich.

## 10. Migrationstest-Stufe

Datenbankmigrationstests prüfen neue Installationen und Upgrades aus repräsentativen historischen Schemaständen.

Die Stufe prüft:

- Deterministische Migrationsreihenfolge
- Erfolgreiche Erstellung einer neuen Datenbank
- Erhalt vorhandener Geschäftsdatensätze
- Erforderliche Backfills und Constraints
- Wiederholten Start nach abgeschlossener Migration
- Kontrollierten Fehler bei nicht unterstützten Zuständen

Private Migrationen und Datenbank-Snapshots werden in diesem Repository nicht veröffentlicht.

## 11. Architektur- und Policy-Prüfungen

Architekturtests oder statische Prüfungen schützen Grenzen, die gewöhnliche Feature-Tests möglicherweise nicht erkennen.

Beispiele sind:

- UI-Code besitzt keine Domänenentscheidungen
- Domänen-Policies hängen nicht von JavaFX oder Connector-Payloads ab
- Geschützte Vorgänge verwenden zentrale Funktionsbewertung
- Transaktionsverantwortung bleibt in Application Services
- Verbotene Abhängigkeiten überschreiten keine Modulgrenzen
- Öffentliche Artefakte enthalten keine bekannten sensiblen Muster

## 12. Headless-JavaFX-Validierung

JavaFX-Tests können eine grafische Umgebung benötigen, auch wenn kein physisches Display verfügbar ist. Die CI-Umgebung kann für unterstützte UI- und Integrationstests ein kontrolliertes virtuelles Display wie Xvfb bereitstellen.

Headless-Validierung berücksichtigt:

- Deterministischen Start des Toolkits
- Isolation zwischen Tests
- Bereinigung von Fenstern und Anwendungsthreads
- Timeouts für blockierte UI-Arbeit
- Trennung visueller Renderingtests von Domänentests

## 13. Testebenen

| Ebene | Feedbackgeschwindigkeit | Hauptzweck |
| --- | --- | --- |
| Unit | Schnell | Reine Logik und Policy-Verträge |
| Repository | Schnell bis mittel | Persistenzabfragen und Constraints |
| Integration | Mittel | Zusammenarbeit von Services, Transaktionen und Adaptern |
| Migration | Mittel | Schemaentwicklung und Datenerhalt |
| UI/Headless | Mittel bis langsam | Desktopinteraktion und JavaFX-Integration |
| Regression | Breit | Schutz vor bereits beobachteten Fehlern |

Die Pipeline kann unabhängige Ebenen parallelisieren und zugleich eine eindeutige Endfreigabe bewahren.

## 14. Qualitätsgrenze

Die Merge-Grenze kombiniert automatisierte und menschliche Nachweise.

Automatisierte Nachweise sind:

- Erfolgreicher Build
- Erforderliche Tests bestanden
- Migrationsvalidierung bestanden
- Keine verbotenen Architekturverletzungen
- Erforderliche Artefakte erzeugt

Menschliche Nachweise sind:

- Anforderungen und Akzeptanzkriterien geprüft
- Architekturauswirkungen verstanden
- Sicherheits- und Datenschutzgrenze geprüft
- Dokumentation und Diagramme visuell kontrolliert
- Finale Freigabe erfasst

## 15. Fehlerbehandlung

Eine fehlgeschlagene Stufe stoppt oder blockiert den abhängigen Pfad und stellt handlungsfähige Diagnosen bereit.

Fehlerberichte sollen identifizieren:

- Stufe und Testkategorie
- Sichere Fehlerzusammenfassung
- Relevanten Protokoll- oder Artefaktort
- Gegebenenfalls Reproduktionsbefehl
- Deterministischen, umgebungsbezogenen oder externen Fehler

Secrets und personenbezogene Daten werden maskiert oder aus Protokollen ausgeschlossen.

## 16. Caching

Caching von Abhängigkeiten kann Build-Zeit verkürzen, darf aber nicht zu einer unerklärten Korrektheitsquelle werden.

Cache-Schlüssel berücksichtigen:

- Betriebsumgebung
- Java-Version
- Abhängigkeitsdefinitionen
- Relevante Build-Konfiguration

Ein sauberer Build bleibt der Referenzpfad zur Diagnose cachebezogener Fehler.

## 17. Artefakte

Pull-Request-Artefakte können sichere Engineering-Ausgaben enthalten, beispielsweise:

- Testberichte
- Coverage-Zusammenfassungen, soweit verwendet
- Ergebnisse statischer Analysen
- Gerenderte Dokumentationsvorschauen
- Nicht produktive Diagnosepakete

Produktive Installer, Signaturmaterial, private Konfiguration und Kundendaten werden nicht an öffentliche Workflows angehängt.

## 18. Secret-Verwaltung

Secrets werden nur Stufen bereitgestellt, die sie benötigen, und niemals in der Versionsverwaltung gespeichert.

Kontrollen umfassen:

- Umgebungszugriff nach Least-Privilege
- Geschützte Release-Umgebungen
- Keine Secret-Offenlegung an nicht vertrauenswürdigen Pull-Request-Code
- Maskierte Protokolle
- Rotation und Widerruf außerhalb des Repositorys
- Getrennte Testzugangsdaten für kontrollierte Integrationsumgebungen

Dieses Dokument nennt keine produktiven Anbieter oder Secret-Bezeichner.

## 19. Abhängigkeits- und Supply-Chain-Kontrollen

Die Pipeline kann Kontrollen für Abhängigkeiten und Build-Integrität enthalten:

- Geprüfte Abhängigkeitsänderungen
- Versions- und Herkunftsprüfung
- Schwachstellenscans
- Begrenzte Veröffentlichungsberechtigungen
- Reproduzierbare Release-Eingaben
- Nachvollziehbare Zuordnung von Commit zu Artefakt

Ergebnisse werden bewertet und nicht automatisch unterdrückt.

## 20. Trennung des Releases

Continuous Integration und Release besitzen getrennte Verantwortlichkeiten.

Ein erfolgreicher Pull-Request-Build weist nach, dass die Änderung die erforderliche Verifikation erfüllt. Er autorisiert nicht automatisch ein produktives Release.

Release-Vorbereitung kann erfordern:

- Freigegebene Version
- Sauberen Commit auf `main`
- Vollständiges Regressionsergebnis
- Release Notes
- Packaging-Prüfung
- Signatur in geschützter Umgebung
- Explizite menschliche Freigabe

## 21. Kontrollen des Dokumentations-Repositorys

Dieses öffentliche Architektur-Repository enthält Dokumentation und nicht die produktive Anwendung.

Seine Validierungsprioritäten sind:

- Korrekte Markdown-Links
- Ausgeglichene und renderbare Mermaid-Blöcke
- Zweisprachige Struktur
- Keine Secrets oder privaten Daten
- Fokussierter Dateiumfang
- Visuelle Prüfung der GitHub-Darstellung

Das bewusste Fehlen produktiver Workflow-Definitionen bedeutet nicht, dass die CI der privaten Anwendung öffentlich ist.

## 22. Grenze der KI-unterstützten Entwicklung

KI kann Implementierung, Tests, Refactoring oder Dokumentation vorschlagen, genehmigt aber nicht die eigenen Ergebnisse.

Der CI-Ablauf liefert unabhängige ausführbare Nachweise. Menschliche Prüfung bleibt verantwortlich für:

- Anforderungen
- Architekturentscheidungen
- Sicherheits- und Datenschutzfreigabe
- Interpretation von Fehlern
- Annahme erzeugter oder geänderter Tests
- Finale Merge- und Release-Entscheidungen

## 23. Metriken und Wartung

Nützliche Pipeline-Metriken können umfassen:

- Build-Dauer
- Fehlerrate je Stufe
- Wiederkehr instabiler Tests
- Zeit zur Wiederherstellung eines grünen `main`-Branches
- Migrationstestabdeckung unterstützter Versionen

Metriken unterstützen Wartungsentscheidungen und ersetzen kein technisches Urteil.

## 24. Tests der Pipeline

Pipeline-Änderungen werden selbst geprüft und validiert.

Sichere Praktiken sind:

- Kleine Workflow-Änderungen
- Test-Branches oder kontrollierte Probeläufe
- Standardmäßig minimale Berechtigungen
- Prüfung von Fehlerpfaden
- Kontrolle von Trigger- und Secret-Umfang
- Rollback-Plan für Release-Workflow-Änderungen

## 25. Bewusste Nicht-Ziele

Die öffentliche Fallstudie veröffentlicht nicht:

- Produktive Workflow-Dateien
- Signatur- oder Deployment-Zugangsdaten
- Private Artefaktziele
- Release-Schlüssel oder Installer-Konfiguration
- Interne Repository-Namen
- Kundenumgebungen oder betriebliche Endpunkte

## 26. Verwandte Dokumentation

- [Technologiestack](02-technology-stack.de.md)
- [Systemarchitektur](03-system-architecture.de.md)
- [Datenbankdesign](05-database-design.de.md)
- [Lizenzarchitektur](08-license-architecture.de.md)
- [CI-Pipeline-Diagramm](../diagrams/ci-pipeline.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
