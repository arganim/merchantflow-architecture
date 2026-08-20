# Lizenzarchitektur

## 1. Zweck und Umfang

Dieses Dokument beschreibt die konzeptionelle Lizenz- und Funktionsarchitektur von MerchantFlow. Es erklärt, wie Edition, Berechtigung, Betriebsmodus und Laufzeitnachweise in konsistente Anwendungsentscheidungen übersetzt werden.

Das öffentliche Modell enthält bewusst keine Signaturschlüssel, kryptografischen Implementierungsdetails, Manipulationsschutzmechanismen, produktiven Lizenzformate, Validierungsendpunkte oder betrieblichen Umgehungswege.

## 2. Architekturziele

Die Lizenzarchitektur unterstützt:

- Zentrale Funktionsentscheidungen
- Konsistentes Verhalten von Oberfläche und Application Services
- Fail-closed-Behandlung ungültiger oder unsicherer geschützter Zustände
- Klare Trennung von Lizenzierung und Geschäftsregeln
- Desktop- und verbindungstoleranten Betrieb
- Testbare Entscheidungen ohne echtes Lizenzmaterial
- Verständliche benutzerbezogene Ergebnisse
- Minimale Offenlegung von Lizenz- und Geräteinformationen
- Kontrollierte Übergänge zwischen Betriebsmodi

## 3. Zentrale Konzepte

| Konzept | Verantwortung | Wichtige Grenze |
| --- | --- | --- |
| Edition | Beschreibt eine produktbezogene Funktionsmenge | Editionsnamen autorisieren nicht selbstständig Vorgänge |
| Berechtigung | Repräsentiert ein genehmigtes Recht oder Abonnement | Wird erst nach Validierung der Nachweise akzeptiert |
| Funktion | Benennt einen geschützten Anwendungsvorgang oder eine Funktionsgruppe | Oberfläche und Services verwenden dieselbe Funktionsidentität |
| Lizenznachweis | Liefert signierte oder anderweitig vertrauenswürdige Berechtigungsdaten | Rohe Nachweise werden nicht in der Anwendung verteilt |
| Betriebsmodus | Beschreibt normales, begrenztes, Evaluations-, abgelaufenes oder nicht verfügbares Verhalten | Modusübergänge folgen expliziter Policy |
| Funktionsentscheidung | Liefert erlauben oder verweigern mit sicherer Begründungskategorie | Unsicherheit wird nicht zu Berechtigung |
| Lizenzkontext | Liefert begrenzte Laufzeitfakten für die Bewertung | Sensible Geräte- und Kontodaten werden minimiert |

Die genauen produktiven Namen und Werte bleiben privat.

## 4. Zentrale Bewertungsgrenze

Lizenzentscheidungen erfolgen über eine zentrale Funktionsbewertungsgrenze. Oberflächen, Controller, Application Services und Adapter interpretieren rohe Lizenzdateien nicht unabhängig voneinander.

Der Evaluator erhält konzeptionell:

- Angeforderte Funktion
- Validierte Berechtigungsinformationen
- Aktuellen Betriebsmodus
- Begrenzten Laufzeitkontext
- Relevante Policy-Version

Er liefert eine strukturierte Entscheidung, die sicher protokolliert, getestet und dargestellt werden kann.

## 5. Funktionsbasiertes Design

Anwendungscode fragt nach der Verfügbarkeit einer Funktion, statt im gesamten Code Editionsnamen zu vergleichen.

Dieses Design ermöglicht:

- Eine Stelle zur Zuordnung von Editionen zu Funktionen
- Geringere Auswirkungen bei Änderungen der Produktpakete
- Klare Autorisierung in Services
- Einfachere Policy-Tests
- Konsistente UI-Sichtbarkeit und Meldungen

Eine Edition ist eine Konfiguration von Funktionen und keine Sammlung verteilter `if`-Anweisungen.

## 6. Mehrschichtige Durchsetzung

Funktionsentscheidungen werden auf mehreren Ebenen durchgesetzt.

### 6.1 Präsentationsschicht

Die Oberfläche verwendet die Entscheidung, um:

- Optionale Navigation anzuzeigen oder auszublenden
- Controls zu aktivieren oder zu deaktivieren
- Die Nichtverfügbarkeit einer Operation zu erklären
- Unmögliche Abläufe nicht zu starten

Der UI-Zustand verbessert die Bedienbarkeit, ist aber nicht der verbindliche Schutz.

### 6.2 Application-Service-Schicht

Der Application Service prüft die Funktion erneut, bevor er einen geschützten Anwendungsfall ausführt.

Dies verhindert:

- Umgehung über eine andere Oberfläche
- Aufruf durch Hintergrund- oder Integrationscode
- Autorisierung durch veralteten UI-Zustand
- Direkte Aufrufe ohne Präsentationskontrollen

### 6.3 Infrastrukturgrenze

Adapter erhalten nur freigegebene Befehle. Sie entscheiden nicht über Produktberechtigungen und können eine verweigerte Funktion nicht freischalten.

## 7. Bewertungsablauf

Eine konzeptionelle Funktionsprüfung folgt diesen Phasen:

1. Angeforderte Funktion identifizieren.
2. Begrenzten Lizenzzustand über einen vertrauenswürdigen Provider laden.
3. Nachweise und erforderlichen Kontext validieren.
4. Aktuellen Betriebsmodus bestimmen.
5. Berechtigungen in Funktionen auflösen.
6. Policy anwenden und strukturierte Erlaubnis oder Verweigerung liefern.
7. Dieselbe Entscheidung an UI- und Servicegrenzen durchsetzen.

Das [Diagramm zur Lizenzbewertung](../diagrams/license-evaluation.de.md) stellt diesen Ablauf dar.

## 8. Fail-closed-Policy

Geschützte Vorgänge werden verweigert, wenn der Evaluator keine gültige Freigabe feststellen kann.

Fail-closed-Bedingungen können sein:

- Fehlender erforderlicher Nachweis
- Ungültiger oder nicht lesbarer Nachweis
- Nicht unterstützte Policy oder Formatversion
- Abgelaufene Berechtigung
- Widersprüchlicher Laufzeitkontext
- Fehlgeschlagene Verifikation
- Interner Evaluatorfehler

Benutzer erhalten eine sichere Begründung und gegebenenfalls einen Wiederherstellungshinweis. Interne Sicherheitsdetails erscheinen nicht in Fehlermeldungen.

## 9. Betriebsmodi

Das öffentliche Modell verwendet konzeptionelle Modi:

- Normalbetrieb
- Begrenzter oder schreibgeschützter Betrieb
- Evaluationsbetrieb
- Abgelaufen oder erneuerungsbedürftig
- Nicht verfügbar, weil der Zustand nicht geprüft werden kann

Die genauen produktiven Modi und Übergänge bleiben privat. Ein Modus definiert den Policy-Kontext; einzelne Vorgänge fragen weiterhin explizite Funktionen ab.

## 10. Desktop- und Offline-Aspekte

MerchantFlow ist Desktop-First und soll bei vorübergehender Netzwerkunterbrechung gegebenenfalls weiter nutzbar bleiben.

Die Architektur trennt:

- Lokale Validierung von Nachweisen
- Optionale entfernte Aktualisierung oder Synchronisierung
- Zeitlich begrenzten früher validierten Zustand
- Offline weiterhin verfügbare Vorgänge
- Vorgänge mit aktueller Bestätigungspflicht

Offline-Verhalten ist Policy-gesteuert. Ein Netzwerkfehler gewährt nicht automatisch Zugriff, und vorübergehende Nichtverfügbarkeit beschädigt keine vorhandenen Geschäftsdaten.

## 11. Zeit- und Uhrbehandlung

Zeitabhängige Berechtigungen benötigen bewusste Behandlung, weil lokale Uhren falsch eingestellt oder verändert sein können.

Die Architektur behandelt Zeit als explizite Eingabe und testet:

- Grenzzeitpunkte
- Ablaufübergänge
- Früher validierten Zustand
- Eindeutig inkonsistente Zeitbeobachtungen
- Wiederherstellung nach gültiger Aktualisierung

Dieses Dokument veröffentlicht weder Erkennung von Uhrmanipulation noch Karenzzeiten.

## 12. Caching

Funktionsentscheidungen dürfen nur innerhalb einer definierten Gültigkeitsgrenze gecacht werden.

Das Cache-Design berücksichtigt:

- Policy- und Berechtigungsversion
- Änderungen des Betriebsmodus
- Aktualisierungsereignisse
- Anwendungsneustart
- Ablauf- oder Validierungsgrenzen
- Explizite Invalidierung nach Lizenzänderungen

Veraltete gecachte Berechtigung darf den zugrunde liegenden Zustand nicht überleben.

## 13. Verantwortung für Lizenzzustand

Ein dedizierter Provider besitzt das Laden und Interpretieren des persistierten Lizenzzustands. Andere Module erhalten validierte Sichten oder Funktionsentscheidungen.

Dies begrenzt:

- Offenlegung rohen Lizenzmaterials
- Doppelte Parsing-Logik
- Inkonsistente Fehlerbehandlung
- Unbeabsichtigte Persistenz abgeleiteten Sicherheitszustands
- Abhängigkeit von Domänenobjekten von Speicherformaten

## 14. Trennung von Geschäftsregeln

Die Lizenzierung entscheidet, ob ein Anwendungsfall verfügbar ist. Domänenregeln entscheiden, ob der angeforderte Geschäftsvorgang gültig ist.

Beide Bedingungen müssen erfüllt sein.

Eine verfügbare Dokumentfunktion macht beispielsweise eine ungültige Bestellung nicht dokumentberechtigt. Umgekehrt umgeht eine gültige Bestellung keine fehlende Funktion.

## 15. Strukturierte Entscheidungen

Der Evaluator liefert eine Entscheidung mit begrenzten Informationen, beispielsweise:

- Erlaubt oder verweigert
- Angeforderte Funktion
- Sichere Begründungskategorie
- Aktueller öffentlicher Betriebsmodus
- Ob eine Benutzeraktion die Verfügbarkeit wiederherstellen kann

Die Entscheidung enthält keine Schlüssel, Signaturen, Gerätefingerprints oder internen Verifikationsspuren.

## 16. Fehler und Benutzererlebnis

Lizenzbezogene Ergebnisse müssen verständlich sein, ohne Sicherheitsinternas offenzulegen.

Benutzerbezogene Kategorien können unterscheiden:

- Funktion nicht enthalten
- Lizenz muss erneuert werden
- Zustand kann aktuell nicht verifiziert werden
- Konfiguration oder Installation benötigt Unterstützung
- Vorgang ist im aktuellen Modus nicht verfügbar

Technische Protokolle verwenden Korrelation und sichere Kategorien statt vollständiger Lizenz-Payloads.

## 17. Aktualisierung und Erneuerung

Die Lizenzaktualisierung ist ein kontrollierter Anwendungsvorgang.

Konzeptionell:

1. Neue Nachweise über einen genehmigten Weg beziehen.
2. Vor Ersetzung des akzeptierten Zustands validieren.
3. Neuen Zustand bei Bedarf atomar persistieren.
4. Abgeleitete Funktionscaches invalidieren.
5. Betriebsmodus neu bewerten.
6. Strukturiertes Ergebnis liefern.

Eine fehlgeschlagene Aktualisierung ersetzt bekannte gültige Nachweise nicht still durch unbrauchbare Daten.

## 18. Widerruf und Herabstufung

Wenn Berechtigungen entfallen, schützt die Architektur vorhandene Geschäftsdaten.

Die Policy kann neue geschützte Vorgänge einschränken und zugleich angemessenen Zugriff auf vorhandene Datensätze erlauben. Eine Herabstufung löscht keine Dokumente, Bestellungen oder Kundenhistorie.

Das konkrete kommerzielle Herabstufungsverhalten bleibt außerhalb dieser öffentlichen Fallstudie.

## 19. Sicherheitsgrenze

Sicherheitsrelevante Komponenten sind hinter engen Schnittstellen isoliert.

Die öffentliche Dokumentation enthält nicht:

- Signatur- oder Verifikationsschlüssel
- Kryptografische Algorithmen und Parameter
- Eingaben zur Gerätebindung
- Interne Integritätsprüfungen
- Lizenzdateistruktur
- Entfernte Validierungsrouten
- Debug- oder Support-Umgehungen

Die Architektur vermittelt Verantwortlichkeiten, ohne eine Implementierungsanleitung zur Umgehung bereitzustellen.

## 20. Datenschutz

Die Lizenzbewertung minimiert Erfassung und Verbreitung von Konto-, Geräte- und Umgebungsinformationen.

Diagnosen vermeiden unnötige Kennungen. Öffentliche Beispiele verwenden synthetische Werte und enthalten keine realen Lizenzdatensätze oder Kundeninformationen.

## 21. Teststrategie

Repräsentative Tests umfassen:

- Durch Berechtigung enthaltene und ausgeschlossene Funktionen
- Konsistenz der Durchsetzung in Oberfläche und Services
- Fehlende, ungültige, abgelaufene und nicht unterstützte Nachweise
- Übergänge von Betriebsmodi
- Offline- und Aktualisierungsverhalten
- Cache-Invalidierung
- Zeitgrenzen
- Fehler ohne teilweise geschützte Operation
- Erhalt bestehender Geschäftsdaten nach Herabstufung

Tests verwenden kontrollierte Fakes und synthetische Nachweise, niemals produktive Schlüssel.

## 22. Bewusste Nicht-Ziele

Diese öffentliche Architektur definiert nicht:

- Vollständiges Digital-Rights-Management-System
- Schutz allein durch ausgeblendete UI-Controls
- Lizenzprüfungen in beliebigem Geschäftscode
- Automatische Berechtigung bei unsicherer Validierung
- Löschung von Geschäftsdaten nach Berechtigungsänderungen
- Öffentliche Support-Umgehungen oder Wiederherstellungs-Secrets

## 23. Verwandte Dokumentation

- [Systemarchitektur](03-system-architecture.de.md)
- [Domänenmodell](04-domain-model.de.md)
- [CI/CD-Pipeline](09-ci-cd-pipeline.de.md)
- [Diagramm zur Lizenzbewertung](../diagrams/license-evaluation.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
