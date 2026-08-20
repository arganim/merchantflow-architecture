# Teststrategie

## 1. Zweck und Umfang

Dieses Dokument beschreibt die konzeptionelle Teststrategie hinter MerchantFlow. Es erklärt, wie Geschäftsregeln, Persistenz, Migrationen, Integrationen, JavaFX-Abläufe und Regressionsverhalten an geeigneten Grenzen geprüft werden.

Die öffentliche Fallstudie enthält keinen privaten Quellcode, Test-Fixtures, Datenbank-Snapshots, Kundendaten, externe Zugangsdaten oder vollständige produktive Testsuiten.

## 2. Qualitätsprinzipien

Die Strategie folgt diesen Prinzipien:

- Verträge und beobachtbares Verhalten testen
- Geschäftsregeln unabhängig von JavaFX und Infrastruktur halten
- Die kleinste wirksame Testebene verwenden
- Fehlerverhalten ebenso bewusst wie Erfolgsverhalten prüfen
- Historische Daten durch Regression und Migrationstests schützen
- Synthetische und deterministische Testdaten verwenden
- Instabile Tests als Defekte behandeln
- CI-Nachweise unabhängig von KI-erzeugter Implementierung halten
- Testqualität und nicht nur Testanzahl prüfen

## 3. Mehrschichtiges Testmodell

| Testebene | Primärer Gegenstand | Hauptnachweis |
| --- | --- | --- |
| Unit | Reine Berechnungen, Normalisierung und kleine Komponenten | Deterministisches Eingabe-Ausgabe-Verhalten |
| Policy | Domänenentscheidungen und Statusübergänge | Entscheidungstabellen, Invarianten und Fail-closed-Ergebnisse |
| Repository | Abfragen, Mappings, Beziehungen und Constraints | Persistenzvertrag gegen die ausgewählte Datenbank-Engine |
| Application Service | Orchestrierung von Anwendungsfällen und Transaktionsgrenzen | Vollständiges akzeptiertes oder abgelehntes Operationsergebnis |
| Integration | Zusammenarbeit von Spring, Adaptern und Infrastruktur | Korrekte Grenzübersetzung und Fehlerbehandlung |
| Migration | Neues Schema und unterstützte historische Upgrades | Datenerhalt und deterministische Entwicklung |
| UI/Headless | JavaFX-Interaktion und Präsentationsintegration | Verhalten von Benutzerabsicht, Zustand und Rückmeldung |
| Regression | Zuvor beobachtete Risiken über mehrere Ebenen | Schutz vor wiederkehrenden Defekten |

Das [Diagramm zur Teststrategie](../diagrams/testing-strategy.de.md) zeigt, wie diese Ebenen in einer Freigabeentscheidung zusammenlaufen.

## 4. Unit-Tests

Unit-Tests prüfen kleines deterministisches Verhalten, ohne den vollständigen Anwendungskontext zu starten.

Repräsentative Gegenstände sind:

- Text- und Adressnormalisierung
- Geld- und Mengenberechnungen
- Mapping-Hilfen
- Datums- und Zeitgrenzen
- Fehlerklassifikation
- Formatierungsregeln ohne Rendering-Infrastruktur

Unit-Tests sollen schnell genug für kontinuierliche Ausführung während der Entwicklung bleiben.

## 5. Domänen-Policy-Tests

Policy-Tests prüfen Geschäftsentscheidungen mit expliziten Szenarien.

Beispiele sind:

- Nachweise zur Kundenidentität
- Entscheidungen über Adressversionen
- Ergebnisse der Bestellduplikatprüfung
- Gültige und ungültige Statusübergänge
- Dokumentberechtigung
- Erlaubte und verweigerte Funktionsentscheidungen
- Mehrdeutigkeit und Fail-closed-Verhalten

Entscheidungsorientierte Tests verwenden benannte Szenarien und prüfen strukturierte Ergebnisse statt privater Implementierungsaufrufe.

## 6. Application-Service-Tests

Application-Service-Tests prüfen die Orchestrierung von Anwendungsfällen.

Sie prüfen, dass der Service:

- Alle erforderlichen Nachweise lädt
- Die richtigen Policies aufruft
- Vor Mutation einen Plan erstellt
- Die vorgesehene Transaktionsgrenze öffnet
- Repositories und Adapter in erforderlicher Reihenfolge koordiniert
- Ein strukturiertes Ergebnis liefert
- Nach Fehlern keinen Teilzustand hinterlässt

Kontrollierte Fakes können externe Nebeneffekte isolieren und zugleich den Anwendungsvertrag bewahren.

## 7. Repository-Tests

Repository-Tests prüfen Persistenzverhalten gegen SQLite, soweit datenbankspezifisches Verhalten relevant ist.

Die Abdeckung umfasst:

- Abfrageergebnisse und Reihenfolge
- Laden erforderlicher Beziehungen
- Quellenbezogene Eindeutigkeit
- Mapping von Kennungen und Werttypen
- Constraint-Verhalten
- Transaktionssichtbarkeit
- Erhalt historischer Datensätze

Mocks ersetzen keine Repository-Tests für Verhalten, das Datenbank oder ORM-Mapping besitzen.

## 8. Migrationstests

Migrationstests prüfen die Schemaentwicklung aus unterstützten Zuständen.

Wichtige Szenarien sind:

- Erstellung einer neuen Datenbank
- Upgrade aus repräsentativen historischen Versionen
- Backfill erforderlicher Werte
- Erstellung von Constraints und Indizes
- Erhalt von Kunden-, Bestell- und Dokumentbeziehungen
- Wiederholter Start nach erfolgreicher Migration
- Kontrollierter Fehler bei inkonsistentem oder nicht unterstütztem Zustand

Test-Fixtures sind synthetisch und enthalten keine produktiven Informationen.

## 9. Integrationstests

Integrationstests prüfen die Zusammenarbeit mehrerer realer Grenzen.

Repräsentative Szenarien sind:

- Zusammensetzung des Spring Context
- Zusammenarbeit von Service und Repository
- Transaktions-Commit und -Rollback
- Connector-Mapping mit lokalen Fixtures
- Verträge der Dokumenterstellung
- Verhalten von Datei- und Exportgrenzen
- Integration des Lizenzproviders mit kontrollierten Nachweisen

Externe Produktivsysteme werden durch Fakes, Stubs oder dedizierte Testumgebungen ersetzt.

## 10. Connector-Vertragstests

Connectoren übersetzen nicht vertrauenswürdige externe Formate in interne Requests.

Vertragstests prüfen:

- Mapping erforderlicher und optionaler Felder
- Nicht unterstützte oder fehlende Werte
- Erhalt der Quellidentität
- Übersetzung technischer Fehler
- Soweit unterstützt Kompatibilität von Payload-Versionen
- Keine Geschäftsmutation während des Parsings

Reale Kontokennungen und Payloads werden nicht in öffentlichen Fixtures gespeichert.

## 11. JavaFX- und Headless-Tests

JavaFX-Tests konzentrieren sich auf Präsentationsverantwortung:

- Benutzerabsicht erreicht den richtigen Anwendungsfall
- View-Zustand spiegelt strukturierte Ergebnisse wider
- Controls werden konsistent aktiviert oder deaktiviert
- Validierungsfeedback ist sichtbar und handlungsfähig
- Hintergrundarbeit blockiert den Anwendungsthread nicht fehlerhaft
- Fenster und Anwendungsthreads werden nach Tests bereinigt

CI kann für unterstützte Headless-Ausführung ein virtuelles Display wie Xvfb bereitstellen.

## 12. Dokument- und Exporttests

Dokumente und Exporte sind Geschäftsausgaben und keine beliebigen UI-Dateien.

Tests können prüfen:

- Berechtigungsprüfung erfolgt vor Rendering
- Erforderliche Transaktionswerte werden verwendet
- Ausgabestruktur ist deterministisch
- Policy für doppelte Ausgabe wird eingehalten
- Fehler erzeugen keinen irreführenden Erfolgszustand
- Personenbezogene Daten werden nur für den freigegebenen Geschäftszweck ausgegeben

Öffentliche Tests enthalten keine realen Rechnungen, Labels, Kundenadressen oder Carrier-Konten.

## 13. Sicherheits- und Datenschutztests

Sicherheitsbezogene Tests prüfen Architekturkontrollen wie:

- Geschützte Vorgänge benötigen Funktionsentscheidungen
- Ungültige Lizenznachweise verhalten sich fail-closed
- Secrets werden nicht in Protokolle geschrieben
- Nicht vertrauenswürdige Eingaben werden vor Persistenz validiert
- Pfade und Dateinamen bleiben innerhalb freigegebener Grenzen
- Unnötige personenbezogene Daten werden aus Diagnosen ausgeschlossen
- Prüfungen des öffentlichen Repositorys erkennen bekannte sensible Muster

Tests reduzieren Risiko, sind aber keine eigenständige Sicherheitszertifizierung.

## 14. Tests von Fehlerpfaden

Fehlerverhalten ist Bestandteil des Vertrags.

Tests prüfen bewusst:

- Ungültige und unvollständige Eingaben
- Mehrdeutige Kundenidentität
- Konflikte doppelter externer Bestellungen
- Datenbank-Constraint-Fehler
- Transaktionsrollback
- Gesperrten oder nicht verfügbaren lokalen Speicher
- Connector-Timeout oder fehlerhafte Antwort
- Fehler bei Dokument-, E-Mail- oder Exportzustellung
- Nicht unterstützten Migrationszustand

Ein Fehlertest prüft sowohl das gemeldete Ergebnis als auch das Fehlen unbeabsichtigter Mutation.

## 15. Nebenläufigkeits- und Idempotenztests

Das Desktop-Modell besitzt begrenzte Nebenläufigkeit, aber Hintergrundimporte und wiederholte Befehle können sich überlappen.

Tests umfassen:

- Wiederholte Benutzeraktion
- Doppelte Connector-Zustellung
- Zwei Versuche mit derselben externen Identität
- Erneute Zustandsprüfung vor Commit
- Sichere Wiederholung nach technischem Fehler
- Keine zweite akzeptierte Transaktion nach idempotenter Wiederholung

## 16. Testdatenstrategie

Testdaten sind synthetisch, minimal und zweckbezogen.

Prinzipien sind:

- Keine kopierten Produktionsdatenbanken
- Keine realen Kunden- oder Partnerinformationen
- Explizite Builder oder Fixtures
- Bei Bedarf stabile Daten, Kennungen und Währungen
- Klare Grenzwerte
- Getrennte gültige, ungültige und mehrdeutige Szenarien
- Einfache Diagnose bei fehlgeschlagenem Fixture

## 17. Determinismus

Tests kontrollieren Quellen von Nichtdeterminismus:

- Zeit und Uhren
- Zufällige Kennungen
- Locale und Zeitzone
- Dateisystemorte
- Externe Netzwerkaufrufe
- Soweit möglich Thread-Scheduling
- Initialzustand der Datenbank

Eine instabile Abhängigkeit wird hinter einer kontrollierbaren Grenze gekapselt.

## 18. Testisolation

Jeder Test besitzt seinen relevanten Zustand und bereinigt nach der Ausführung.

Isolation verhindert:

- Reihenfolgeabhängige Ergebnisse
- Verunreinigung gemeinsam genutzter Datenbanken
- Verbliebene JavaFX-Threads oder Fenster
- Wiederverwendete Ausgabedateien
- Versteckte Abhängigkeit von lokaler Entwicklerkonfiguration

## 19. Regressionsstrategie

Wenn ein Defekt behoben wird, hält ein fokussierter Regressionstest den kleinsten stabilen Vertrag fest, der ihn erkannt hätte.

Regressionssuiten sind um Risiken organisiert wie:

- Identitäts- und Duplikatauflösung
- Schutz historischer Daten
- Statusübergänge
- Dokumentzuordnung
- Datenbank-Upgrades
- Funktionsdurchsetzung
- Externes Mapping

Die Anzahl der Tests ist ein Engineering-Signal und kein alleiniger Korrektheitsnachweis.

## 20. Policy für instabile Tests

Ein instabiler Test gilt als Defekt in Test oder Systemgrenze.

Die Reaktion umfasst:

- Fehlgeschlagenes Szenario erfassen
- Zeit-, Nebenläufigkeits-, Umgebungs- oder Cleanup-Abhängigkeit identifizieren
- Mit kontrollierten Eingaben reproduzieren
- Nur mit expliziter Verantwortung korrigieren oder isolieren
- Erforderliche Qualitätsgrenze zeitnah wiederherstellen

Wiederholte blinde Neuausführung gilt nicht als gültige Abnahmestrategie.

## 21. Coverage- und Mutationsevidenz

Coverage kann nicht ausgeführte Bereiche identifizieren, misst aber weder Assertion-Qualität noch fachliche Vollständigkeit.

Soweit sinnvoll können Mutation oder Vertragsprüfung Tests erkennen, die Code ausführen, ohne fehlerhaftes Verhalten zu erkennen. Ziele sind risikobasiert und kein isolierter universeller Prozentwert.

## 22. CI-Ausführungsstrategie

Schnelle Unit- und Policy-Tests liefern frühes Feedback. Repository-, Integrations-, Migrations- und JavaFX-Tests laufen als erforderliche Merge-Grenzen.

Unabhängige Gruppen können parallel ausgeführt werden. Alle erforderlichen Ergebnisse laufen vor Annahme des Pull Requests zusammen.

## 23. KI-unterstützte Testentwicklung

KI kann Szenarien, Fixtures, Assertions und Refactoring vorschlagen, aber erzeugte Tests benötigen menschliche Prüfung.

Die Prüfung stellt sicher, dass ein Test:

- Einen realen Vertrag darstellt
- Aus dem vorgesehenen Grund fehlschlagen kann
- Nicht nur die Implementierung spiegelt
- Sichere synthetische Daten verwendet
- Relevante Grenzfälle abdeckt
- Wartbar bleibt

CI liefert unabhängige Ausführungsevidenz; KI genehmigt nicht die eigenen Ergebnisse.

## 24. Akzeptanzkriterien

Eine Änderung ist merge-bereit, wenn:

- Erforderliche fokussierte Tests bestehen
- Erforderliche Regressionsgruppen bestehen
- Bei Schemaänderungen Migrationsverhalten validiert ist
- Neues Fehlerverhalten abgedeckt ist
- Testdaten synthetisch und sicher bleiben
- Kein unerklärtes instabiles Ergebnis offen bleibt
- Menschliche Prüfung Architektur und Akzeptanzkriterien bestätigt

## 25. Öffentliche Abstraktionsgrenze

Dieses Dokument enthält bewusst nicht:

- Privaten Testquellcode und Fixtures
- Produktive Datenbank-Snapshots
- Reale Kunden-, Lizenz- oder Marketplace-Daten
- Interne Coverage-Berichte und Defektmetriken
- Sicherheits-Testpayloads, die Umgehung ermöglichen könnten
- Produktive CI-Workflow-Definitionen

## 26. Verwandte Dokumentation

- [CI/CD-Pipeline](09-ci-cd-pipeline.de.md)
- [Systemarchitektur](03-system-architecture.de.md)
- [Datenbankdesign](05-database-design.de.md)
- [Sicherheit und Datenschutz](11-security-and-privacy.de.md)
- [Diagramm zur Teststrategie](../diagrams/testing-strategy.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
