# Bestellablauf

## 1. Zweck und Umfang

Dieses Dokument beschreibt den konzeptionellen MerchantFlow-Bestellablauf von Erfassung oder Import über Validierung, Annahme, Buchung und Erfüllungsausgaben bis zur Korrektur.

Es erläutert Orchestrierung und Sicherheitsgrenzen, ohne produktive Zustände, Connector-Payloads, Steueralgorithmen, Dokumentnummernregeln oder wirtschaftlich sensible Abläufe zu veröffentlichen.

## 2. Ziele des Ablaufs

Der Bestellablauf unterstützt:

- Einen kontrollierten Prozess für manuelle und externe Bestellungen
- Frühe Duplikaterkennung
- Explizite Kunden-, Adress- und Produktzuordnung
- Geschützte Transaktionswerte
- Gültige Statusübergänge
- Atomare lokale Persistenz
- Bewusste Behandlung externer Nebeneffekte
- Nachvollziehbare Dokument- und Versandausgaben
- Fail-closed-Verhalten bei Mehrdeutigkeit und ungültigem Zustand
- Testbare Ergebnisse in jeder Phase

## 3. Eingangskanäle

| Eingangskanal | Initiale Darstellung | Wichtige Kontrolle |
| --- | --- | --- |
| Manuelle Erfassung | Vom Benutzer eingegebener Application Request | UI-Validierung ersetzt keine Domänenvalidierung |
| Marketplace-Import | Connector-spezifische Transport-Payload | Payload wird vor Annahme gemappt und normalisiert |
| Wiederholte Synchronisierung | Bereits bekannte externe Identität und aktuelle Payload | Idempotenz und Duplikatentscheidung erfolgen vor Mutation |
| Kontrollierte Korrektur | Expliziter Befehl gegen eine vorhandene Bestellung | Historische Nachvollziehbarkeit und Berechtigung bleiben erhalten |

Alle Kanäle führen in interne Anwendungsverträge. Connector-Modelle und JavaFX-Controls werden nicht direkt zu Domänendatensätzen.

## 4. Überblick

Der öffentliche Ablauf umfasst folgende Phasen:

1. Eingaben erfassen oder abrufen.
2. Transportspezifische Felder in einen internen Request übertragen.
3. Werte für Vergleich und Validierung normalisieren.
4. Externe Identität auflösen und Duplikate erkennen.
5. Kunden- und Adressentscheidungen treffen.
6. Produkte zuordnen und Bestellpositionen validieren.
7. Kaufmännische und lebenszyklusbezogene Vorbedingungen prüfen.
8. Einen akzeptierten Persistenzplan erstellen.
9. Die vollständige lokale Transaktion committen.
10. Nur zulässige Dokumente, Nachrichten oder Versandausgaben auslösen.

Das [Bestellablaufdiagramm](../diagrams/order-flow.de.md) zeigt diese Reihenfolge und die wichtigsten Blockierungsergebnisse.

## 5. Erfassung und Mapping

Eingaben werden zunächst in einen Application Request überführt, der Transportaufgaben von fachlicher Bedeutung trennt.

Mapping-Aufgaben können umfassen:

- Lesen von Quellfeldern
- Konvertierung technischer Typen
- Erhalt der Quellidentität
- Erkennung fehlender Pflichtangaben
- Klassifizierung von Transportfehlern
- Erfassung sicherer Herkunftsmetadaten

Mapping darf nicht still Kunden erstellen, Bestellungen buchen oder Lebenszyklusregeln umgehen.

## 6. Normalisierung

Normalisierung erzeugt vergleichbare Darstellungen und bewahrt zugleich die erforderlichen ursprünglichen Geschäftswerte.

Beispiele sind:

- Bereinigung und Vereinheitlichung von Benutzereingaben
- Normalisierung von Länder- und Postdarstellungen
- Standardisierte Vergleichsformen für E-Mail- oder Telefondaten
- Konvertierung von Mengen und Geldwerten in definierte interne Typen
- Zuordnung externer Statuswerte zu einer Anwendungsinterpretation

Normalisierung ist deterministisch und testbar. Sie ist kein Nachweis dafür, dass zwei Datensätze demselben Kunden gehören.

## 7. Duplikaterkennung

Die Duplikatvermeidung erfolgt vor Erstellung einer neuen lokalen Bestellung.

Nachweise können umfassen:

- Quellkanal zusammen mit externer Bestellidentität
- Bereits akzeptierte Synchronisierungsreferenz
- Vorhandene lokale Transaktionsidentität
- Kontrollierten Fingerprint nur als unterstützenden Nachweis

Die stärkste Regel ist die quellenbezogene externe Identität. Ähnliche Namen, Summen oder Adressen reichen nicht aus, um Bestellungen zusammenzuführen.

Konzeptionelle Ergebnisse sind:

- Neue Bestellkandidatin
- Vorhandene Bestellung bereits akzeptiert
- Vorhandene Bestellung benötigt Synchronisierungsprüfung
- Identitätskonflikt blockiert die Verarbeitung

## 8. Kundenzuordnung

Der Bestellrequest besitzt nicht die Regeln zur Kundenzuordnung. Er delegiert an eine explizite Customer-Resolution-Policy.

Diese Policy kann entscheiden:

- Geprüften vorhandenen Kunden wiederverwenden
- Neuen Kunden erstellen
- Benutzerprüfung verlangen
- Mehrdeutige Zuordnung blockieren

Kein Fallback wählt still den ersten ähnlichen Datensatz.

## 9. Adresszuordnung

Rechnungs- und Lieferrollen werden explizit bewertet. Aktuelle Kundenadressen und Transaktionsadressen besitzen unterschiedliche Lebenszyklen.

Der Ablauf kann:

- Eine geeignete aktuelle Adressversion wiederverwenden
- Eine neue Kundenadressversion erstellen
- Getrennte Rechnungs- oder Lieferrollen bewahren
- Bestellungsbezogene historische Adressdatensätze erzeugen
- Eine ungültige oder unvollständige Adresse blockieren

Historische Bestelladressen werden bei späteren Änderungen an Kundenstammdaten nicht ersetzt.

## 10. Produkt- und Positionszuordnung

Jede Bestellposition wird vor Annahme der Bestellung validiert.

Prüfungen können umfassen:

- Produktidentität oder kontrolliertes Fallback-Verhalten
- Gültigkeit von Menge und Einheit
- Preis- und Steuerkontext
- Erforderliche Betriebsmerkmale
- Versandbezogene Vollständigkeit
- Doppelte oder widersprüchliche Positionsinformationen

Die akzeptierte Position hält die für spätere Dokumente und Auswertungen benötigten Transaktionswerte fest.

## 11. Kaufmännische Validierung

Anwendungs- und Domänenregeln bewerten die Konsistenz der vollständigen Bestellung.

Repräsentative Prüfungen sind:

- Mindestens eine gültige Position ist vorhanden.
- Erforderliche Kunden- und Adressentscheidungen sind vollständig.
- Geldwerte verwenden gültige Darstellungen.
- Summen und Positionskontext sind intern konsistent.
- Quellidentität ist nicht fehlerhaft gebunden.
- Der angeforderte Initialstatus ist zulässig.
- Erforderliche Funktionen sind verfügbar.

Die konkreten Preis- und Steuerregeln bleiben außerhalb dieser öffentlichen Fallstudie.

## 12. Planung vor Mutation

MerchantFlow trennt Entscheidungsfindung und Persistenz. Die Validierung erzeugt einen strukturierten Plan, bevor Geschäftsdatensätze geschrieben werden.

Der Plan beschreibt konzeptionell:

- Welche vorhandenen Datensätze wiederverwendet werden
- Welche neuen Datensätze entstehen
- Welche historischen Werte in die Bestellung übernommen werden
- Welche Beziehungen hergestellt werden
- Welcher Statusübergang angefordert wird
- Welcher Vorgang warum blockiert ist

Nur ein akzeptierter Plan erreicht die Mutationsphase.

## 13. Transaktionsgrenze

Die lokale Datenbanktransaktion umfasst den zusammengehörigen Zustand, der zur konsistenten Annahme einer Bestellung erforderlich ist.

Innerhalb der Transaktion kann die Anwendung:

- Freigegebene Kundenstammdaten erstellen oder aktualisieren
- Erforderliche Adressversionen erzeugen
- Bestellung und Positionen persistieren
- Transaktionsadressen persistieren
- Quellenbezogene externe Referenzen binden
- Lebenszyklus- und Auditmetadaten erfassen

Wenn ein erforderlicher Schreibvorgang fehlschlägt, wird die lokale Operation zurückgerollt, statt einen unvollständigen Geschäftsdatensatz zu hinterlassen.

## 14. Statusübergänge

Der öffentliche Ablauf verwendet konzeptionelle Lebenszykluskategorien:

- Entwurf oder importiert
- Validiert
- Akzeptiert oder gebucht
- In Erfüllung
- Abgeschlossen
- Storniert oder korrigiert

Übergänge sind explizite Befehle. Sie prüfen aktuellen Zustand, erforderliche Daten, Funktionsentscheidungen und frühere Ausgaben, bevor sie die Bestellung ändern.

Direkte Statusänderungen aus Oberfläche, Connector-Mapping oder Dokumentcode sind nicht zulässig.

## 15. Buchung

Die Buchung kennzeichnet eine kontrollierte geschäftliche Festlegung. Sie ist nicht gleichbedeutend mit dem Speichern eines Formulars.

Buchungsvoraussetzungen können umfassen:

- Vollständige Identitäts- und Adressentscheidungen
- Gültige Bestellpositionen und Summen
- Zulässiger vorheriger Lebenszykluszustand
- Keine ungelösten Duplikatkonflikte
- Erforderliche Konfiguration und Funktionsverfügbarkeit

Nach der Buchung dürfen geschützte Transaktionswerte nicht wie gewöhnliche Stammdaten bearbeitet werden.

## 16. Dokumente und Erfüllungsausgaben

Dokumente, Druck, E-Mail und Versandexporte sind Nebeneffekte aus akzeptiertem Geschäftszustand.

Jeder Ausgabevorgang prüft:

- Berechtigung der Bestellung
- Vollständigkeit erforderlicher Daten
- Frühere Ausgabe oder Zustand doppelter Übergabe
- Gewählte Ausgabekonfiguration
- Zuordnung zu Bestellung und Vorgang

Datenbankerfolg und externe Zustellung werden getrennt erfasst, wenn sie nicht dieselbe Transaktion teilen können.

## 17. Stornierung und Korrektur

Eine Stornierung ist ein expliziter Geschäftsvorgang und keine Löschung.

Der Ablauf schützt:

- Bezug zur ursprünglichen Transaktion
- Berechtigung des aktuellen Zustands
- Zuordnung von Stornodokumenten
- Erhalt früherer Dokumente und Ausgaben
- Eindeutiges Ergebnis, wenn eine Stornierung nicht möglich ist

Korrekturen folgen einem kontrollierten Ersetzungs- oder Versionierungsweg. Sie schreiben abgeschlossene Historie nicht still um.

## 18. Idempotenz und Wiederholung

Wiederholte Connector-Zustellung oder Benutzerwiederholung darf nicht unbeabsichtigt eine zweite akzeptierte Bestellung erzeugen.

Der Ablauf nutzt stabile Quellidentität und persistierten Operationszustand zur Unterscheidung von:

- Sicherer Wiederholung einer abgeschlossenen Anfrage
- Fortsetzbarem technischem Fehler
- Neuem Geschäftsvorgang
- Widersprüchlicher Payload mit Prüfbedarf

Wiederholungsverhalten ist explizit und begrenzt. Technische Wiederholung schwächt die fachliche Validierung nicht.

## 19. Fehlerbehandlung

Fehler werden in strukturierte Kategorien eingeordnet, beispielsweise:

- Ungültige Eingabe
- Mehrdeutige Identität
- Duplikatkonflikt
- Unzulässiger Statusübergang
- Fehlende Funktion oder Konfiguration
- Persistenzfehler
- Externer Transport- oder Ausgabefehler

Benutzer erhalten handlungsfähige Ergebnisse, während sensible interne Details und externe Zugangsdaten geschützt bleiben.

## 20. Nebenläufigkeit

Das Desktop-Betriebsmodell besitzt begrenzte lokale Nebenläufigkeit. Wiederholte Klicks, Hintergrundimporte und überlappende Vorgänge können dennoch konkurrieren.

Schutzmaßnahmen umfassen:

- Transaktionale Eindeutigkeit externer Identität
- Erneute Prüfung des relevanten Zustands vor Commit
- Kurze Transaktionsgrenzen
- Kontrollierten Zustand von UI-Aktionen
- Explizite Behandlung belegter oder widersprüchlicher Ergebnisse

## 21. Sicherheit und Datenschutz

Die Bestellverarbeitung behandelt personenbezogene und kaufmännische Daten. Der Ablauf begrenzt die Verbreitung roher Payloads und vermeidet die Protokollierung unnötiger personenbezogener Informationen.

Secrets, Connector-Zugangsdaten, produktive Endpunkte, Kundendatensätze und reale Bestell-Payloads sind nicht Teil dieses Repositorys.

## 22. Teststrategie

Repräsentative Tests umfassen:

- Annahme manueller und externer Bestellungen
- Mapping- und Normalisierungsgrenzen
- Duplikat- und Idempotenzentscheidungen
- Ergebnisse der Kunden- und Adresszuordnung
- Produkt- und Positionsvalidierung
- Gültige und ungültige Statusübergänge
- Transaktionsrollback bei Fehlern
- Berechtigung für Dokumente und Versand
- Nachvollziehbarkeit der Stornierung
- Wiederholung nach technischem Fehler

## 23. Öffentliche Abstraktionsgrenze

Dieses Dokument enthält bewusst nicht:

- Vollständige produktive Zustandsautomaten
- Reale Marketplace-Formate und Kennungen
- Proprietäre Steuer-, Preis- und Nummerierungsregeln
- Exakte Wiederholungszeitpläne und betriebliche Schwellwerte
- Produktive Datenbank- und Package-Namen
- Kunden- oder Transaktionsbeispiele aus realen Daten

## 24. Verwandte Dokumentation

- [Domänenmodell](04-domain-model.de.md)
- [Datenbankdesign](05-database-design.de.md)
- [Kunden- und Adressversionierung](07-customer-address-versioning.de.md)
- [Bestellablaufdiagramm](../diagrams/order-flow.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
