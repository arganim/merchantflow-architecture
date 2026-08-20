# Roadmap

## 1. Zweck und Umfang

Diese Roadmap beschreibt die Entwicklung des MerchantFlow-Architekturportfolios und Entscheidungsthemen für zukünftiges Product Engineering.

Sie ist kein Lieferungsversprechen, Verkaufsangebot, Release-Kalender oder Offenlegung des privaten Produkt-Backlogs. Termine, kommerzielle Prioritäten, Kundenanfragen und sicherheitsrelevante Arbeiten bleiben außerhalb des öffentlichen Repositorys.

## 2. Roadmap-Prinzipien

Die Roadmap folgt diesen Prinzipien:

- Architekturentscheidungen gehen breiter Umsetzung voraus
- Schutz historischer Daten bleibt Release-Bedingung
- Qualitätsgrenzen wachsen mit dem Produktrisiko
- Externe Integrationen bleiben hinter kontrollierten Adaptern
- Sicherheit und Datenschutz sind kontinuierliche Aufgaben
- Roadmap-Themen benötigen Nachweise vor einer Zusage
- Öffentliche Dokumentation bleibt bereinigt und aktuell

## 3. Lieferstatus des Portfolios

| Phase | Umfang | Status |
| --- | --- | --- |
| Grundlage | Produktübersicht, Veröffentlichungsgrenze, zweisprachige README | Abgeschlossen |
| Technologie und Architektur | Stack-Begründung, Systemarchitektur, Kontext | Abgeschlossen |
| Domäne und Persistenz | Domänenmodell, Datenbankdesign, konzeptionelle Beziehungen | Abgeschlossen |
| Geschäftsabläufe | Bestellablauf, Kundenidentität, Adressversionierung | Abgeschlossen |
| Governance | Lizenzarchitektur und CI/CD-Kontrollen | Abgeschlossen |
| Qualität und Schutz | Teststrategie, Sicherheit und Datenschutz | Abgeschlossen |
| Engineering-Prozess | KI-unterstützter Entwicklungsablauf und Roadmap | Mit dieser Phase abgeschlossen |

Das Portfolio kann weiterentwickelt werden, aber der initiale Satz der Architektur-Fallstudie ist vollständig.

## 4. Abgeschlossene Dokumentationsmenge

Der vollständige Satz umfasst:

- Produktproblem und Umfang
- Technologieentscheidungen
- Schichten- und Modularchitektur
- Domänenkonzepte und Persistenzprinzipien
- Bestell-, Kunden- und Adressabläufe
- Lizenz- und Funktions-Governance
- CI/CD- und Teststrategie
- Sicherheits- und Datenschutzgrenzen
- Menschlich gesteuertes, KI-unterstütztes Engineering
- Zukünftige Entscheidungsthemen

Alle wesentlichen Portfolio-Dokumente sind auf Deutsch und Englisch verfügbar.

## 5. Themen der Produktentwicklung

Zukünftige Produktarbeit kann in Themen statt vorschnelle Feature-Versprechen gegliedert werden.

Mögliche Themen sind:

- Tiefere Abdeckung operativer Abläufe
- Zusätzliche Adapter externer Kanäle
- Verbesserte Diagnosen und Supportfähigkeit
- Zuverlässigeres Packaging und Updates
- Reifere Berichte und Exporte
- Barrierefreiheit und Internationalisierung
- Härtung von Sicherheit und Datenschutz
- Performance- und Datenmengenvalidierung
- Erweiterungspunkte mit kontrollierten Verträgen

Jedes Thema benötigt eine getrennte Entscheidung und Akzeptanzgrenze.

## 6. Entscheidungsgrenze für neue Arbeit

Bevor ein Roadmap-Thema verbindliche Arbeit wird, erfolgt eine Bewertung von:

- Benutzer- und Geschäftswert
- Auswirkungen auf das Domänenmodell
- Erforderlichen Datenmigrationen
- Sicherheits- und Datenschutzfolgen
- Lizenz- und Editionsauswirkungen
- Integrations- und Betriebskomplexität
- Test- und Supportkosten
- Rollback- oder Wiederherstellungsstrategie

## 7. Reife der Geschäftsabläufe

Zukünftige Verbesserungen können sich konzentrieren auf:

- Klarere Behandlung von Ausnahmefällen
- Bessere Prüfwarteschlangen für mehrdeutige Importe
- Stärkere Sichtbarkeit von Idempotenz und Wiederholung
- Verbesserte Nachvollziehbarkeit von Dokumenten und Versand
- Konsistente Sicherheit von Massenoperationen
- Weniger manuelle Wiederholung ohne versteckte Entscheidungen

Automatisierung bleibt durch explizite Geschäftsregeln begrenzt.

## 8. Reife der Integrationen

Neue Connectoren sollen die bestehende Adaptergrenze wiederverwenden.

Eine Integration ist erst bereit mit:

- Definierter Quellidentität
- Mapping- und Normalisierungsvertrag
- Duplikat- und Wiederholungsverhalten
- Fehlerklassifikation
- Sicherer Behandlung von Zugangsdaten
- Synthetischen Vertrags-Fixtures
- Beobachtbarkeit ohne Offenlegung personenbezogener Daten
- Klarer Verantwortung und Supportweg

## 9. Reife von Daten und Migrationen

Die Datenbankentwicklung bleibt auf versionierten Migrationen aufgebaut.

Zukünftige Arbeit kann umfassen:

- Breitere Fixtures für Upgrade-Pfade
- Performance-Prüfung größerer Datenmengen
- Verbesserte Wiederherstellungsdiagnosen
- Aufbewahrungs- und Anonymisierungsabläufe
- Sicherere Import- und Exportvalidierung
- Klare Kompatibilitäts-Policy unterstützter Versionen

Kein Roadmap-Thema umgeht den Schutz historischer Daten.

## 10. Qualitätsreife

Qualitätsverbesserungen können umfassen:

- Risikobasierte Regressionsauswahl
- Bessere Diagnose instabiler Tests
- Zusätzliche Architekturprüfungen
- Vertragstests für neue Connectoren
- Baselines für Performance und Ressourcennutzung
- Deterministischere JavaFX-Tests
- Zusammenfassungen von Release-Nachweisen

Metriken bleiben unterstützende Nachweise und keine automatische Freigabe.

## 11. Sicherheitsreife

Sicherheitsarbeit ist kontinuierlich und kann umfassen:

- Regelmäßige Prüfung des Bedrohungsmodells
- Verbesserungen an Abhängigkeiten und Supply Chain
- Übungen zur Rotation von Secrets
- Tests von Sicherung und Wiederherstellung
- Weniger Datenoffenlegung in Diagnosen
- Prüfung von Datei- und Pfadgrenzen
- Regression der Funktionsdurchsetzung
- Übungen zur Vorfallsbereitschaft

Sensible Implementierungsdetails bleiben privat.

## 12. Datenschutzreife

Datenschutzorientierte Entwicklung kann umfassen:

- Prüfung des Dateninventars
- Dokumentation von Zweck und Aufbewahrung
- Kontrollierte Korrektur- und Löschabläufe
- Minimierung von Exporten
- Bessere datenschutzsichere Diagnosen
- Prüfung neuer externer Datenübertragungen
- Pflege synthetischer Testdaten

Rechtliche Interpretation benötigt qualifizierte Prüfung für den tatsächlichen Einsatzkontext.

## 13. Desktop-Betrieb

Reife eines Desktopprodukts kann umfassen:

- Zuverlässigkeit des Installers
- Sicheres Update- und Rollback-Verhalten
- Diagnosen zum Speicherort
- Hinweise zur Sicherung
- Klarheit bei Offline- und Degraded-Modi
- Kompatibilitätstests der Umgebung
- Supportpakete ohne Secrets und unnötige personenbezogene Daten

## 14. Performance und Skalierung

Performance-Arbeit wird durch gemessene Abläufe gesteuert.

Mögliche Nachweise sind:

- Startzeit
- Verhalten großer Bestellungen und Auswertungen
- Importdurchsatz
- Datenbank-Query-Pläne
- Speicherbedarf bei Dokumenterstellung
- UI-Reaktionsfähigkeit bei Hintergrundarbeit

Die Architektur vermeidet Skalierungsbehauptungen ohne repräsentative Messungen.

## 15. Barrierefreiheit und Internationalisierung

Zukünftige UI-Arbeit kann verbessern:

- Tastaturnavigation
- Fokusverhalten
- Verständliche Validierungsmeldungen
- Textskalierung und robuste Layouts
- Vollständigkeit von Übersetzungen
- Locale-bezogene Datums-, Zahlen- und Währungsdarstellung
- Trennung von Benutzersprache und Geschäftsdaten

## 16. Beobachtbarkeit und Supportfähigkeit

Betrieblicher Support kann reifen durch:

- Zustands- und Konfigurationszusammenfassungen
- Sichtbarkeit des Migrationszustands
- Sichere Korrelationskennungen
- Strukturierte Fehlerkategorien
- Handlungsfähige Wiederherstellungshinweise
- Datenschutzgeprüfte Support-Exporte

Diagnosen müssen nützlich bleiben, ohne ein Datenleck zu werden.

## 17. Packaging und Release

Release-Reife kann umfassen:

- Reproduzierbares Packaging
- Geschützte Signaturstufen
- Installer-Validierung
- Versions- und Kompatibilitätsmetadaten
- Release Notes zu akzeptierten Änderungen
- Wiederherstellungshinweise
- Trennung von CI-Erfolg und Release-Autorisierung

## 18. Architecture Decision Records

Zukünftige folgenreiche Entscheidungen sollen als kurze Architecture Decision Records festgehalten werden.

Kandidaten sind Entscheidungen zu:

- Neuen Integrationsmustern
- Speicher- oder Deployment-Änderungen
- Update-Strategie
- Öffentlichen Erweiterungsverträgen
- Aufbewahrungsverhalten
- Wesentlichen Abhängigkeitsänderungen

Ein ADR hält Kontext, Entscheidung, Alternativen und Folgen fest, ohne sensible Betriebsdetails offenzulegen.

## 19. Pflege des Portfolios

Das öffentliche Repository benötigt laufende Pflege:

- Zweisprachige Dokumente synchron halten
- Links nach Strukturänderungen prüfen
- Mermaid-Darstellung erneut kontrollieren
- Veraltete Aussagen entfernen
- Roadmap-Status bewusst aktualisieren
- Veröffentlichungsschutzprüfung wiederholen
- Keine produktiven Beispiele aus Bequemlichkeit hinzufügen

## 20. Veröffentlichungsbereitschaft

Vor öffentlicher Freigabe prüft die finale Grenze:

- Dokumentationssatz ist vollständig
- README-Navigation ist korrekt
- Mermaid-Diagramme werden richtig dargestellt
- Keine Secrets, personenbezogenen Daten oder produktiven Kennungen sind enthalten
- Urheberrecht und fehlende Open-Source-Lizenz bleiben eindeutig
- Repository-Beschreibung und Sichtbarkeit sind beabsichtigt
- Öffentliches Zeitfenster und spätere Privatisierung sind verstanden

## 21. Zukünftige öffentliche Änderungen

Zukünftige öffentliche Aktualisierungen sollen denselben Ablauf verwenden:

1. Fokussierten Branch erstellen.
2. Bereinigte zweisprachige Inhalte hinzufügen.
3. Links, Formatierung und Veröffentlichungsgrenze prüfen.
4. Draft-Pull-Request öffnen.
5. Visuelle Prüfung durchführen.
6. Erst nach expliziter Abnahme mergen.

## 22. Bewusste Nicht-Zusagen

Diese Roadmap verspricht nicht:

- Liefertermine
- Bestimmte Kunden oder Partner
- Benannte Marketplace-Integrationen
- Preis- oder Editionsänderungen
- Öffentliche Freigabe des Quellcodes
- Bestimmtes Hosting- oder Deployment-Modell
- Compliance-Zertifizierung

## 23. Abschlusskriterien

Das initiale Portfolio ist vollständig, wenn:

- Dokumente 01 bis 13 in beiden Sprachen existieren
- Zentrale Diagramme verlinkt und darstellbar sind
- Veröffentlichungsgrenze durchgesetzt ist
- `main` die geprüften Inhalte enthält
- Kein offener inhaltlicher Pull Request verbleibt
- Finaler Repository-Audit besteht
- Sichtbarkeitsänderung nur nach bewusster finaler Freigabe erfolgt

## 24. Verwandte Dokumentation

- [Produktübersicht](01-product-overview.de.md)
- [Systemarchitektur](03-system-architecture.de.md)
- [CI/CD-Pipeline](09-ci-cd-pipeline.de.md)
- [Teststrategie](10-testing-strategy.de.md)
- [Sicherheit und Datenschutz](11-security-and-privacy.de.md)
- [KI-unterstützter Entwicklungsablauf](12-ai-development-workflow.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
