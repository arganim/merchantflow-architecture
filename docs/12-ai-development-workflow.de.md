# KI-unterstützter Entwicklungsablauf

## 1. Zweck und Umfang

Dieses Dokument erklärt, wie KI im MerchantFlow-Entwicklungsprozess als Engineering-Assistent eingesetzt wird, während Anforderungen, Architektur, Risikoentscheidungen, Prüfung und finale Freigabe in menschlicher Verantwortung bleiben.

Es beschreibt Governance und Qualitätskontrollen und veröffentlicht keine privaten Prompts, Quelltexte, Repository-Inhalte, Kundendaten, Zugangsdaten oder proprietären Implementierungskontexte.

## 2. Grundprinzip

Die Beteiligung von KI überträgt keine Engineering-Verantwortung.

Das leitende Prinzip lautet:

> KI darf vorschlagen, transformieren und analysieren. Ein Mensch definiert den Vertrag, kontrolliert den Zugriff, prüft Nachweise und akzeptiert oder verwirft das Ergebnis.

## 3. Verantwortungsmodell

| Menschliche Verantwortung | Unterstützung durch KI |
| --- | --- |
| Produktziele und Prioritäten | Klärungsfragen und alternative Formulierungen |
| Anforderungen und Grenzen | Vorschläge zur Zerlegung von Anforderungen |
| Architekturentscheidungen | Analyse von Abwägungen und Optionen |
| Domäneninvarianten | Vorschläge zu Grenzfällen und Entscheidungstabellen |
| Sicherheits- und Datenschutzgrenzen | Risikofragen und Prüfchecklisten |
| Akzeptanzkriterien | Vorschläge zu Testszenarien |
| Tool- und Datenberechtigungen | Ausführung innerhalb der gewährten Grenze |
| Prüfung von Code und Tests | Diff-Analyse und Defekthypothesen |
| Finale Merge- und Release-Freigabe | Zusammenfassung von Nachweisen |

KI wird niemals Eigentümer einer Anforderung, Architekturentscheidung, Sicherheitsausnahme oder eines Releases.

## 4. Ablauf im Überblick

Der kontrollierte Prozess folgt diesen Phasen:

1. Problem und Geschäftsergebnis definieren.
2. Grenzen und Veröffentlichungsregeln festhalten.
3. Architekturentscheidung treffen oder bestätigen.
4. Akzeptanz- und Fehlerkriterien definieren.
5. Änderung in eine kleine prüfbare Aufgabe zerlegen.
6. Nur erforderlichen Kontext und notwendige Tools freigeben.
7. KI innerhalb der Grenze Vorschlag oder Umsetzung erstellen lassen.
8. Vollständigen Diff und Annahmen prüfen.
9. Fokussierte Tests und erforderliche Regression ausführen.
10. Auf Basis der Nachweise überarbeiten, verwerfen oder akzeptieren.
11. Committen und in CI validieren.
12. Nur mit expliziter menschlicher Freigabe mergen oder releasen.

Das [Diagramm zum KI-Entwicklungsablauf](../diagrams/ai-workflow.de.md) visualisiert diesen Prozess.

## 5. Anforderungen vor dem Prompting

KI-Arbeit beginnt erst, wenn der Mensch einen ausreichenden Vertrag festgelegt hat.

Die Aufgabendefinition enthält:

- Gewünschtes Ergebnis
- Dateien oder Komponenten im Umfang
- Explizite Nicht-Ziele
- Domäneninvarianten
- Sicherheits- und Datenschutzgrenzen
- Akzeptanzkriterien
- Erforderliche Validierung
- Erlaubte externe Aktionen

Eine unvollständige Aufgabe kann mit Analyse beginnen, aber die Umsetzung darf keine folgenreichen Produktentscheidungen still erfinden.

## 6. Architekturkontrolle

Architekturentscheidungen werden vor breiter Umsetzung festgehalten, wenn sie Folgendes beeinflussen:

- Modulverantwortung
- Transaktionsgrenzen
- Verhalten historischer Daten
- Verträge externer Integrationen
- Lizenz- und Funktionsdurchsetzung
- Sicherheits- und Datenschutzkontrollen
- Migrationsstrategie

KI kann Alternativen vergleichen, aber der Mensch wählt die Architektur und trägt Verantwortung für die Folgen.

## 7. Aufgabenzerlegung

Große Änderungen werden in prüfbare Phasen geteilt.

Eine gute Aufgabengrenze besitzt:

- Ein wesentliches Ergebnis
- Bekannte Dateien oder Module
- Definierte Ein- und Ausgaben
- Begrenzte Nebeneffekte
- Explizite Tests
- Einen reversiblen Prüfpunkt

Kleine Grenzen reduzieren Kontextoffenlegung, vereinfachen Prüfung und erleichtern Regressionsdiagnose.

## 8. Kontextverwaltung

KI erhält nur den zur Aufgabe erforderlichen Kontext.

Kontextkontrollen umfassen:

- Produktive Zugangsdaten und personenbezogene Daten ausschließen
- Bereinigte Beispiele bevorzugen
- Relevante Schnittstellen statt ganzer fremder Repositorys teilen
- Verbindliche Dokumente identifizieren
- Bekannte Annahmen explizit nennen
- Kontext bei Branch- oder Anforderungsänderungen aktualisieren

Mehr Kontext ist nicht automatisch besser, wenn er private Daten oder widersprüchliche Anweisungen einführt.

## 9. Tool- und Berechtigungsgrenzen

Tools werden nach Aufgabenumfang freigegeben.

Reine Analyse autorisiert nicht automatisch:

- Dateiänderungen
- Commit oder Push
- Erstellung von Pull Requests
- Merge
- Deployment
- Nachrichten oder Veröffentlichung

Schreibaktionen benötigen Autorisierung und Prüfung des genauen Ziels. Breiter Zugriff wird vermieden, wenn ein enger Pfad genügt.

## 10. Datensicherheit

KI-unterstützte Arbeit darf Folgendes weder erhalten noch veröffentlichen:

- Reale Kunden- oder Bestelldaten
- Produktive Datenbankkopien
- Zugangsdaten, Tokens, Schlüssel oder Lizenzmaterial
- Private Connector-Payloads
- Sensible Support-Protokolle
- Interne kommerzielle Regeln außerhalb der freigegebenen Aufgabe

Synthetische Fixtures und bereinigte Architekturbeispiele werden bevorzugt.

## 11. Implementierungsvorschläge

KI kann unterstützen bei:

- Boilerplate
- Refactoring-Vorschlägen
- Mapping- und Validierungscode
- Testfällen
- Prüfung von Migrationen
- Dokumentation
- Diagnosehypothesen
- Lösungsalternativen

Vorschläge werden gegen den Architekturvertrag bewertet und nicht akzeptiert, nur weil sie plausibel oder syntaktisch vollständig sind.

## 12. Behandlung von Annahmen

KI muss wesentliche Annahmen sichtbar machen.

Die menschliche Prüfung fragt:

- Wird die Annahme durch die Anforderung gestützt?
- Verändert sie eine Geschäftsregel?
- Legt sie Daten offen oder verwirft sie?
- Verändert sie eine öffentliche Schnittstelle oder ein Schema?
- Benötigt sie eine dokumentierte Architekturentscheidung?

Nicht belegte folgenreiche Annahmen stoppen die Aufgabe oder führen zurück zur Analyse.

## 13. Diff-Prüfung

Die vollständige Änderung wird vor Annahme geprüft.

Die Prüfung umfasst:

- Exakt geänderte Dateien
- Fremde Änderungen
- Grenz- und Abhängigkeitsänderungen
- Fehler- und Rollback-Verhalten
- Auswirkungen auf Datenmigration
- Sicherheits- und Datenschutzfolgen
- Hinzugefügte oder geänderte Tests
- Richtigkeit der Dokumentation
- Erzeugte Artefakte und Konfiguration

Kleine Diffs sind nicht von der Prüfung ausgenommen.

## 14. Testerzeugung

KI kann Tests vorschlagen oder erzeugen, aber Tests werden nicht automatisch als vertrauenswürdig behandelt.

Die menschliche Prüfung stellt sicher, dass jeder Test:

- Einen realen Vertrag darstellt
- Aus dem vorgesehenen Grund fehlschlagen kann
- Nicht nur Implementierungslogik wiederholt
- Relevante Fehler- und Grenzfälle enthält
- Synthetische Daten verwendet
- Lesbar und wartbar bleibt

## 15. Unabhängige Validierung

Die Validierung ist vom KI-Vorschlag unabhängig.

Nachweise können umfassen:

- Compiler-Ergebnisse
- Fokussierte Unit- und Policy-Tests
- Repository- und Integrationstests
- Migrationstests
- JavaFX-/Headless-Tests
- Regressionssuiten
- Statische und Architekturprüfungen
- Prüfung gerenderter Dokumentation

KI fasst Nachweise zusammen, wandelt aber eine fehlgeschlagene Qualitätsgrenze nicht in eine Freigabe um.

## 16. Regressionskontrolle

Jede akzeptierte Änderung wird gegen zuvor geschütztes Verhalten bewertet.

Die Regressionsauswahl ist risikobasiert und berücksichtigt:

- Domäneninvarianten
- Historische Daten
- Statusübergänge
- Externe Identität und Idempotenz
- Datenbankentwicklung
- Lizenz- und Funktionsverhalten
- Dokumente und Exporte

## 17. Fehler- und Überarbeitungsschleife

Wenn Vorschlag oder Test fehlschlägt:

1. Fehlernachweis bewahren.
2. Einordnen, ob das Problem in Anforderung, Design, Umsetzung, Test oder Umgebung liegt.
3. Nächste Änderung eingrenzen.
4. Fokussierten fehlgeschlagenen Nachweis erneut ausführen.
5. Erforderliche Regression erneut ausführen.
6. Neuen Diff wieder prüfen.

Wiederholtes blindes Prompting ohne Diagnose ist kein kontrollierter Ablauf.

## 18. Halluzination und Unsicherheit

KI kann plausible, aber unbelegte Aussagen erzeugen.

Kontrollen umfassen:

- Verweise auf aktuellen Quelltext oder verbindliche Dokumentation verlangen
- Veränderliche technische Fakten mit Primärquellen prüfen
- Schlussfolgerungen explizit kennzeichnen
- Für Verhalten ausführbare Nachweise bevorzugen
- Erfundenen Klassen, APIs, Migrationen oder Geschäftsregeln widersprechen
- Tatsächlichen Branch-Zustand vor Schreibaktionen erneut lesen

## 19. Sicherheitsprüfung

KI-unterstützte Änderungen erhalten explizite Sicherheitsprüfung, wenn sie Folgendes betreffen:

- Eingabevalidierung
- Authentifizierung oder Secrets
- Lizenzdurchsetzung
- Dateisystempfade
- Externen Transport
- Personenbezogene Daten
- Protokollierung und Diagnosen
- Build- oder Release-Berechtigungen

Sicherheitsrelevante Unsicherheit folgt einer Fail-closed-Entscheidung.

## 20. Datenschutzprüfung

Die Prüfung kontrolliert, ob eine Änderung:

- Zusätzliche personenbezogene Daten erfasst
- Zweck oder Aufbewahrung verändert
- Protokolle oder Exporte erweitert
- Reale Daten in Tests einführt
- Lösch- oder Korrekturverhalten verändert
- Daten einem neuen externen System offenlegt

Öffentliche Dokumentation verwendet bereinigte Beispiele und explizite Veröffentlichungsgrenzen.

## 21. Git-Disziplin

KI-unterstützte Arbeit folgt denselben Git-Kontrollen wie rein menschliche Arbeit:

- Vom vorgesehenen Basis-Branch starten
- Fokussierten Feature-Branch verwenden
- Staged und unstaged Änderungen prüfen
- Nur freigegebenen Umfang committen
- Aussagekräftige Commit-Nachrichten verwenden
- Remote-Branch prüfen
- Einen fokussierten Pull Request öffnen
- Erst nach Qualitätsgrenzen und Freigabe mergen

Automatisierung rechtfertigt kein breites Staging oder fremde Bereinigung.

## 22. Pull-Request-Disziplin

Ein Pull Request dokumentiert:

- Zweck und Umfang
- Wichtige Designentscheidungen
- Ausgeführte Validierung
- Sicherheits- und Veröffentlichungsgrenze
- Verbleibende manuelle Prüfungen

Draft-Zustand wird verwendet, solange Inhalt oder visuelle Verifikation unvollständig sind.

## 23. CI als unabhängiger Nachweis

CI läuft außerhalb der KI-Unterhaltung und liefert reproduzierbare Nachweise gegen den committed Branch.

Erforderliche Fehler blockieren die Annahme. KI kann bei der Diagnose helfen, aber geschützte Checks nicht ohne getrennte Prüfung aufheben oder verändern.

## 24. Dokumentationskontrolle

KI kann bei zweisprachiger und strukturierter Dokumentation unterstützen.

Die Dokumentationsprüfung kontrolliert:

- Technische Richtigkeit
- Übereinstimmung der Sprachen
- Funktionierende Links
- Mermaid-Darstellung
- Keine privaten Namen, Pfade, Daten oder Secrets
- Klare Trennung von Fakt, Design und Zukunftsplan

## 25. Menschliche Abnahmegrenze

Der Mensch akzeptiert eine Änderung erst nach Bestätigung:

- Anforderung ist erfüllt
- Architektur bleibt kohärent
- Domäneninvarianten sind geschützt
- Sicherheits- und Datenschutzgrenzen werden eingehalten
- Tests liefern sinnvolle Nachweise
- Diff enthält nur beabsichtigte Änderungen
- CI und erforderliche manuelle Prüfungen bestehen

## 26. Metriken und Lernen

Nützliche Prozesssignale können sein:

- Prüfungsfunde vor Annahme
- Durch neue Tests verhinderte Regressionen
- Überarbeitung durch unklare Anforderungen
- Instabile Test- oder Umgebungsfehler
- Zeit für Diagnose im Verhältnis zur Umsetzung
- Während visueller Prüfung gefundene Dokumentationsfehler

Metriken verbessern den Prozess, bewerten keine Personen und ersetzen kein Urteil.

## 27. Bewusste Nicht-Ziele

Dieser Ablauf unterstützt nicht:

- Autonome Produktverantwortung durch KI
- Ungeprüfte Codeerzeugung
- KI-Freigabe eigener Tests oder Änderungen
- Weitergabe produktiver Secrets oder Kundendaten
- Umgehung von CI oder menschlicher Abnahme
- Breite externe Aktionen ohne Autorisierung
- Prompt-Qualität als Ersatz für Architektur

## 28. Öffentliche Abstraktionsgrenze

Dieses Dokument enthält bewusst nicht:

- Private Prompts und Unterhaltungsprotokolle
- Inhalte produktiver Repositorys
- Zugangsdaten und Tool-Konfiguration
- Kunden- und Betriebsdaten
- Interne Defekthistorien
- Sicherheitsrelevante Agentenanweisungen
- Kommerzielle Implementierungsdetails

## 29. Verwandte Dokumentation

- [Systemarchitektur](03-system-architecture.de.md)
- [CI/CD-Pipeline](09-ci-cd-pipeline.de.md)
- [Teststrategie](10-testing-strategy.de.md)
- [Sicherheit und Datenschutz](11-security-and-privacy.de.md)
- [Roadmap](13-roadmap.de.md)
- [Diagramm zum KI-Entwicklungsablauf](../diagrams/ai-workflow.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
