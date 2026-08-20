# Sicherheit und Datenschutz

## 1. Zweck und Umfang

Dieses Dokument beschreibt die konzeptionellen Sicherheits- und Datenschutzkontrollen von MerchantFlow sowie die Veröffentlichungsschutzmaßnahmen dieses Architekturportfolios.

Es ist ein Engineering-Überblick und keine Zertifizierung, Rechtsberatung, Penetrationstest-Bericht oder Behauptung vollständiger Compliance in jedem Rechtsraum. Produktive Sicherheitskonfiguration, Zugangsdaten, Schlüssel, Endpunkte, Kundendaten und detaillierte Schutzmechanismen bleiben privat.

## 2. Sicherheitsziele

Die Architektur unterstützt:

- Klare Vertrauensgrenzen
- Zugriff nach Least-Privilege
- Validierung nicht vertrauenswürdiger Eingaben
- Schutz lokaler Geschäftsdatensätze
- Zentrale Durchsetzung geschützter Funktionen
- Kontrollierte Dokumente, Exporte und externe Kommunikation
- Isolation von Secrets
- Sichere Diagnosen und Protokollierung
- Sicherungsbewussten Datenschutz
- Datenschutz durch Datenminimierung und Zweckbindung
- Veröffentlichungskontrollen für das öffentliche Portfolio

## 3. Geschützte Werte

Repräsentative geschützte Werte sind:

- Kundenidentität und Kontaktdaten
- Rechnungs- und Lieferadressen
- Bestellungen, Preise, Steuerkontext und Dokumente
- Marketplace- und E-Mail-Zugangsdaten
- Lokale Datenbank und Sicherungen
- Lizenznachweise und sicherheitsrelevante Konfiguration
- Erzeugte PDFs, Tabellen und Versanddateien
- Build-, Release- und Signaturmaterial

Das öffentliche Repository enthält keine dieser produktiven Werte.

## 4. Datenklassifizierung

| Klassifizierung | Beispiele | Behandlungsprinzip |
| --- | --- | --- |
| Öffentlich | Bereinigte Architekturdokumentation | Vor Veröffentlichung auf Sicherheit geprüft |
| Intern | Nicht geheime Engineering-Details und Diagnosen | Auf autorisierte Entwicklung begrenzt |
| Vertraulich | Kunden-, Bestell-, Partner- und Geschäftsdatensätze | Nur für freigegebene Anwendungszwecke verarbeitet |
| Geheim | Zugangsdaten, Tokens, Signaturschlüssel und geschütztes Lizenzmaterial | Isoliert, minimiert und niemals committed |

Die Klassifizierung beeinflusst Entscheidungen zu Speicherung, Protokollierung, Tests, Export und Veröffentlichung.

## 5. Vertrauensgrenzen

MerchantFlow behandelt folgende Eingaben bis zur Validierung als nicht vertrauenswürdig:

- Vom Benutzer eingegebene Formulardaten
- Marketplace- und Connector-Payloads
- Importierte Dateien
- Konfigurationswerte
- Lizenznachweise
- Dateisystempfade und gewählte Ausgabeorte
- Aus Sicherung wiederhergestellte oder durch Migration aktualisierte Daten

Das [Diagramm der Sicherheitsgrenzen](../diagrams/security-boundaries.de.md) zeigt den wesentlichen Validierungspfad.

## 6. Eingabevalidierung

Validierung erfolgt in der geeigneten Schicht:

- Präsentationsformatierung und Pflichtfeldhinweise in JavaFX
- Anwendungsvorbedingungen in Use-Case-Services
- Fachliche Invarianten in Domänen-Policies
- Technische Constraints in Repositories und Datenbank
- Transportvalidierung in Connectoren und Adaptern

UI-Validierung allein gilt niemals als Sicherheitsgrenze.

## 7. Externe Connectoren

Connectoren isolieren Authentifizierung, Transport, Parsing und Übersetzung technischer Fehler.

Sicherheitskontrollen umfassen:

- Payloads als nicht vertrauenswürdig behandeln
- In begrenzte Transportmodelle parsen
- Vor fachlichem Vergleich normalisieren
- Quellidentität bewahren
- Nicht unterstützte oder fehlerhafte Eingaben ablehnen
- Vollständige Payloads nicht protokollieren
- Timeouts und begrenzte Wiederholung anwenden
- Direkte Domänenmutation durch Transportcode verhindern

## 8. Authentifizierung und Secrets

Zugangsdaten und Tokens werden außerhalb der Versionsverwaltung gespeichert und nur benötigten Komponenten bereitgestellt.

Prinzipien sind:

- Keine Secrets in Repository-Dateien
- Keine Secrets in öffentlichen Beispielen
- Zugriff nach Least-Privilege
- Maskierte Protokolle
- Rotation und Widerruf durch genehmigte Betriebsprozesse
- Getrennte Testzugangsdaten
- Keine Secret-Übertragung durch Anwendungsdiagnosen

Der konkrete Credential Store und die Provider-Konfiguration bleiben privat.

## 9. Lokale Desktop-Sicherheit

MerchantFlow ist eine lokale Desktopanwendung. Ihr Sicherheitsmodell umfasst Anwendungskontrollen und die Host-Betriebsumgebung.

Die Anwendung kontrolliert:

- Auswahl freigegebener Speicherpfade
- Datenbankzugriff über Repositories
- Funktionsdurchsetzung
- Validierungs- und Transaktionsgrenzen
- Kontrollierten Export und Diagnosen

Die Betriebsumgebung bleibt verantwortlich für:

- Zugriff auf Benutzerkonten
- Festplatten- und Geräteschutz
- Dateisystemberechtigungen
- Endpoint Security
- Physischen Zugriff
- Sicheres Sicherungsziel

## 10. Schutz der lokalen Datenbank

Die SQLite-Datenbank wird nicht als öffentliche API bereitgestellt.

Kontrollen sind:

- Zugriff über Application Repositories
- Referenzielle Integrität und Constraints
- Kontrollierte Migrationen
- Kurze Transaktionen
- Koordinierte Sicherung
- Diagnosen ohne unnötige Pfadoffenlegung
- Kein direkter Connector- oder Browserzugriff

Exakte Pfade, Dateinamen, Schemas und Verschlüsselungsentscheidungen werden nicht veröffentlicht.

## 11. Transaktionsintegrität

Sicherheit umfasst auch den Schutz vor inkonsistentem Geschäftszustand.

Application Services:

- Laden erforderliche Nachweise
- Validieren vor Mutation
- Wenden zusammengehörige Schreibvorgänge bei Bedarf atomar an
- Rollen fehlgeschlagene lokale Operationen zurück
- Trennen Datenbankerfolg von Erfolg externer Nebeneffekte
- Liefern explizite Fehlerergebnisse

## 12. Funktionsdurchsetzung

Geschützte Vorgänge verwenden den zentralen Lizenz- und Funktions-Evaluator.

Die Durchsetzung erfolgt in:

- UI-Präsentation für verständliches Benutzererlebnis
- Application Services als verbindlicher Schutz

Ungültiger oder unsicherer geschützter Zustand folgt dem Fail-closed-Prinzip. Detaillierte Lizenzschutzkontrollen bleiben privat.

## 13. Dokumente und Exporte

Dokumente und Exporte können personenbezogene oder wirtschaftlich sensible Daten enthalten.

Kontrollen umfassen:

- Berechtigungsprüfung vor Erzeugung
- Freigegebene Ausgabefelder
- Deterministische Dateibenennung und Ziel-Policy
- Schutz vor doppelter Ausgabe
- Eindeutige Zuordnung zur Quellbestellung
- Fehlerbehandlung ohne falschen Erfolg
- Keine Veröffentlichung realer Ausgabebeispiele

Benutzer bleiben für den Schutz exportierter Dateien nach Übergabe an ihr gewähltes Ziel verantwortlich.

## 14. E-Mail und Nachrichten

E-Mail-Zustellung ist ein externer Nebeneffekt.

Die Anwendung prüft:

- Freigegebenen Geschäftszweck
- Beabsichtigte Empfängerdaten
- Erforderliche Dokumentberechtigung
- Konfigurierte Transportgrenze
- Zustellungsergebnis getrennt vom Transaktionsergebnis

Secrets und vollständige Nachrichteninhalte sind aus Diagnosen ausgeschlossen.

## 15. Protokollierung und Diagnosen

Protokolle sollen nützlich sein, ohne zu einem sekundären Datenspeicher zu werden.

Protokollierungsprinzipien sind:

- Sichere Ereignis- und Begründungskategorien
- Bei Bedarf Korrelationskennungen
- Keine Passwörter, Tokens, Signaturmaterial oder vollständigen Lizenznachweise
- Keine vollständigen Kundenadressen oder externen Payloads, außer streng erforderlich und geschützt
- Begrenzte Aufbewahrung
- Klare Trennung benutzerbezogener Meldungen und technischer Diagnosen

## 16. Fehlerbehandlung

Technische Exceptions werden in strukturierte Anwendungsfehler übersetzt.

Benutzerausgaben sollen:

- Sicheren nächsten Schritt erklären
- Stack Traces und Infrastrukturdetails vermeiden
- Nicht offenlegen, ob sensible Kennungen existieren
- Validierungs-, Autorisierungs-, Persistenz- und Transportfehler unterscheiden

## 17. Sicherungen und Wiederherstellung

Sicherungen enthalten dieselben sensiblen Geschäftsdaten wie die aktive Datenbank.

Die Sicherungsstrategie berücksichtigt:

- Koordinierte Snapshot-Erstellung
- Freigegebenes Ziel
- Zugriffskontrolle
- Integritäts- und Wiederherstellungsvalidierung
- Aufbewahrungs-Policy
- Sichere Entsorgung
- Eindeutige Identifikation ohne übermäßige Pfadoffenlegung

Öffentliche Dokumentation enthält keine Sicherungsorte, Zeitpläne oder Zugangsdaten.

## 18. Migrationssicherheit

Datenbankmigrationen besitzen die Berechtigung, lokales Schema und Daten zu verändern.

Kontrollen umfassen:

- Versionsverwaltete private Migrationsskripte
- Prüfung vor Release
- Deterministische Reihenfolge
- Test-Upgrades aus unterstützten Zuständen
- Fehler vor normaler Geschäftsnutzung bei ungültiger Migration
- Keine dynamischen Migrationsinhalte aus nicht vertrauenswürdigen Quellen

## 19. Abhängigkeits- und Build-Sicherheit

Build- und Abhängigkeitskontrollen können umfassen:

- Geprüfte Abhängigkeitsänderungen
- Bekannte Java- und Maven-Umgebung
- Schwachstellen- und Herkunftsprüfungen
- Minimale CI-Berechtigungen
- Geschützte Release- und Signaturstufen
- Nachvollziehbare Commit-zu-Artefakt-Zuordnung

Ergebnisse werden nach Erreichbarkeit und Risiko bewertet und nicht ignoriert oder als automatischer Kompromittierungsnachweis behandelt.

## 20. Datenschutzprinzipien

MerchantFlow verwendet datenschutzorientierte Engineering-Prinzipien:

- Datenminimierung
- Zweckbindung
- Richtigkeit durch kontrollierte Korrektur
- Speicherbegrenzung durch explizite Aufbewahrungs-Policy
- Integrität und Vertraulichkeit
- Nachvollziehbarkeit wichtiger Vorgänge
- Trennung von Test- und Produktionsdaten

Konkrete rechtliche Pflichten hängen vom Einsatzkontext ab und benötigen qualifizierte Prüfung.

## 21. Datenaufbewahrung und Löschung

Kundenstammdaten, Transaktionshistorie und rechtlich relevante Dokumente können unterschiedliche Aufbewahrungsanforderungen besitzen.

Die Architektur unterscheidet:

- Deaktivierung für zukünftige Nutzung
- Korrektur unrichtiger Daten
- Aufbewahrung von Transaktionsnachweisen
- Datenschutzbedingte Löschung oder Anonymisierung
- Sichere Entsorgung von Sicherungen und Exporten

Löschung ist eine kontrollierte Policy und keine uneingeschränkte Kaskade über einen UI-Button.

## 22. Betroffenenanfragen

Soweit anwendbar können Betriebsprozesse Auskunft, Korrektur, Export, Einschränkung oder Löschung unterstützen.

Die Umsetzung muss berücksichtigen:

- Identitätsprüfung
- Gesetzliche Aufbewahrungspflichten
- Umfang über aktive Daten, Dokumente, Sicherungen und Exporte
- Nachvollziehbarkeit der Antwort
- Vermeidung der Offenlegung an die falsche Person

Dieses Portfolio behauptet keinen universellen Rechtsablauf.

## 23. Sicherheitstests

Repräsentative Sicherheitstests umfassen:

- Ungültige und fehlerhafte externe Eingaben
- Pfad- und Dateigrenzvalidierung
- Funktionsdurchsetzung
- Fail-closed-Lizenzverhalten
- Ausschluss von Secrets und personenbezogenen Daten aus Protokollen
- Transaktionsrollback
- Migrationsfehler
- Duplikat- und Wiederholungsverhalten
- Prüfung des öffentlichen Repositorys auf sensible Muster

Tests ergänzen Designprüfung und Betriebskontrollen.

## 24. Vorfalls- und Wiederherstellungsbereitschaft

Betriebliche Bereitschaft umfasst die Fähigkeit:

- Betroffene Komponente und Datenklasse zu identifizieren
- Unsichere externe Kommunikation zu stoppen
- Relevante Nachweise sicher zu bewahren
- Offengelegte Zugangsdaten zu rotieren oder zu widerrufen
- Aus validierter Sicherung wiederherzustellen
- Korrekturmaßnahmen zu kommunizieren und zu dokumentieren

Produktive Vorfallkontakte und Verfahren bleiben privat.

## 25. Sicherheit des öffentlichen Repositorys

Dieses Repository unterliegt einer Veröffentlichungsgrenze.

Vor Veröffentlichung werden Inhalte geprüft auf:

- Kunden-, Bestell-, Partner- und Mitarbeiterinformationen
- Zugangsdaten, Tokens, Schlüssel und Lizenzmaterial
- Produktive Domains, Endpunkte und Pfade
- Datenbankdateien, Exporte und Screenshots
- Private Repository- und Package-Namen
- Wirtschaftlich sensible Entscheidungsregeln
- Metadaten mit betrieblichen Rückschlüssen

## 26. Bewusste Auslassungen

Dieses öffentliche Dokument enthält nicht:

- Zugangsdaten oder Schlüsselverwaltungsdesign
- Exakte Verschlüsselungs- und Signaturkonfiguration
- Implementierung von Manipulationsschutz
- Interne Threat-Intelligence- oder Erkennungsregeln
- Produktive Netzwerk- und Speichertopologie
- Ergebnisse von Penetrationstests
- Kundenspezifische Kontrollen oder Rechtsberatung

## 27. Verwandte Dokumentation

- [Systemarchitektur](03-system-architecture.de.md)
- [Datenbankdesign](05-database-design.de.md)
- [Lizenzarchitektur](08-license-architecture.de.md)
- [CI/CD-Pipeline](09-ci-cd-pipeline.de.md)
- [Teststrategie](10-testing-strategy.de.md)
- [Diagramm der Sicherheitsgrenzen](../diagrams/security-boundaries.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
