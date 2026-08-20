# Domänenmodell

## 1. Zweck und Umfang

Dieses Dokument beschreibt das konzeptionelle Domänenmodell von MerchantFlow. Es erläutert Geschäftsobjekte, Verantwortungsgrenzen, Identitäten, Regeln für historische Daten und Invarianten der privaten Implementierung.

Das Modell ist bewusst technologieneutral. Produktive Klassen- und Package-Namen, Datenbanktabellen, proprietäre Entscheidungslogik und kundenspezifische Konfigurationen werden nicht veröffentlicht.

## 2. Ziele der Modellierung

Das Domänenmodell unterstützt:

- Eindeutige Verantwortlichkeit für Geschäftsdatensätze
- Verlässliche Kunden- und Bestellidentität
- Trennung veränderbarer Stammdaten von historischen Transaktionen
- Kontrollierte Statusübergänge von Bestellungen
- Nachvollziehbare Dokument- und Versandausgaben
- Sichere Übernahme externer Marketplace-Bestellungen
- Deterministische Duplikatvermeidung
- Zentrale Fail-closed-Entscheidungen
- Testbare Invarianten unabhängig von der Oberfläche

## 3. Zentrale Konzepte

| Konzept | Verantwortung | Wichtige Grenze |
| --- | --- | --- |
| Kunde | Beschreibt die Geschäftsbeziehung zu einer Person oder Organisation | Ein ähnlicher Name ist kein ausreichender Identitätsnachweis |
| Adressversion | Beschreibt eine aktuelle oder frühere Kundenadresse | Historische Versionen werden erhalten und nicht still überschrieben |
| Produkt | Enthält wiederverwendbare kaufmännische und operative Stammdaten | Bestellhistorie darf sich durch spätere Produktänderungen nicht verändern |
| Bestellung | Besitzt die akzeptierte Geschäftstransaktion und ihren Lebenszyklus | Ungültige oder mehrdeutige Übergänge werden blockiert |
| Bestellposition | Hält Produkt, Menge, Preis und Steuerkontext der Bestellung fest | Transaktionswerte sind vor späteren Stammdatenänderungen geschützt |
| Bestelladresse | Hält die für die Transaktion verwendete Rechnungs- oder Lieferadresse fest | Sie gehört zur Bestellhistorie, nicht zum veränderbaren Kundenprofil |
| Geschäftsdokument | Beschreibt Rechnung, Lieferschein, Stornierung oder verwandte Ausgabe | Dokumente benötigen einen zulässigen und zuordenbaren Transaktionszustand |
| Versandexport | Beschreibt geprüfte Daten für Carrier oder Versandabläufe | Wiederholte Ausgabe muss erkennbar und kontrollierbar sein |
| Externe Referenz | Verbindet eine lokale Bestellung mit der Identität eines externen Kanals | Externe Kennungen gelten nur innerhalb ihres Quellsystems |
| Funktionsentscheidung | Beschreibt die Verfügbarkeit einer Operation in Edition und Betriebsmodus | Unsichere geschützte Zustände werden abgelehnt statt geraten |

## 4. Aggregatgrenzen

MerchantFlow nutzt Aggregatgrenzen, um festzulegen, wo Konsistenz geschützt werden muss.

### 4.1 Kundenaggregat

Das Kundenaggregat besitzt die aktuellen Beziehungsdaten und die Historie bekannter Adressen. Es schützt Identitätsnachweise und verhindert die unbeabsichtigte Zusammenführung verschiedener Personen oder Organisationen.

Typische Verantwortlichkeiten sind:

- Stabile interne Kundenidentität
- Normalisierte, aber nicht allein maßgebliche Suchmerkmale
- Kontaktdaten und Herkunft
- Eigentum an Adressversionen
- Aktiv- oder Inaktivkennzeichen
- Explizite Zusammenführungs- oder Korrekturabläufe, soweit unterstützt

### 4.2 Bestellaggregat

Das Bestellaggregat besitzt die akzeptierte Transaktion. Es enthält Positionen, Transaktionsadressen, kaufmännischen Kontext, Herkunft und Lebenszykluszustand, die zur späteren Interpretation erforderlich sind.

Das Aggregat schützt:

- Erforderliche Bestellpositionen
- Akzeptierte Mengen, Preise und Steuerkontexte
- Rechnungs- und Lieferhistorie
- Herkunft und Duplikatidentität
- Zulässige Statusübergänge
- Berechtigung für Buchung, Stornierung, Dokumente und Export

### 4.3 Produktaggregat

Das Produktaggregat besitzt wiederverwendbare Stammdaten für zukünftige Vorgänge. Bestehende Bestellpositionen behalten die für ihre Transaktion akzeptierten Werte, auch wenn ein Produkt später umbenannt, neu bepreist, anders klassifiziert oder deaktiviert wird.

### 4.4 Ausgabedatensätze

Dokumente und Versandexporte werden aus freigegebenem Geschäftszustand abgeleitet, bleiben aber eigenständig nachvollziehbare Datensätze. Sie halten fest, was für welche Transaktion durch welchen kontrollierten Vorgang erzeugt wurde.

## 5. Identitätsstrategie

Interne Identität und Geschäftsidentität sind unterschiedliche Aufgaben.

- Interne Kennungen schaffen stabile Referenzen innerhalb der Anwendung.
- Namen, E-Mail-Adressen und Postanschriften unterstützen Suche und Zuordnung, können sich aber ändern.
- Externe Kennungen sind nur zusammen mit ihrem Quellkanal eindeutig.
- Dokumentnummern folgen kontrollierten Geschäftsregeln und werden nicht als Datenbankidentität verwendet.

Die Kundenzuordnung verwendet explizite Nachweise. Mehrdeutige Ähnlichkeit führt zu Prüfung oder Blockierung und nicht zu einer stillen Zusammenführung.

## 6. Kunden- und Adresshistorie

Ein Kunde kann im Zeitverlauf mehrere Adressversionen besitzen. Die Aktualisierung aktueller Stammdaten darf die in einer früheren Transaktion verwendete Adresse nicht umschreiben.

Das Modell unterscheidet deshalb:

1. Eine kundenbezogene Adressversion für aktuelle oder zukünftige Vorgänge.
2. Einen bestellungsbezogenen Adressdatensatz, der den Transaktionskontext bewahrt.
3. Einen expliziten Korrektur- oder Ersetzungsvorgang, wenn historische Daten rechtmäßig geändert werden müssen.

Diese Trennung unterstützt Dokumentkonsistenz, Nachvollziehbarkeit und sichere Synchronisierung mit externen Kanälen.

## 7. Bestellpositionen und Snapshots

Eine Bestellposition verweist auf ein Produkt und hält zugleich die kaufmännischen Werte fest, die bei Erstellung oder Buchung der Transaktion akzeptiert wurden.

Geschützte Transaktionswerte können umfassen:

- Produktbezeichnung der Bestellung
- Menge und Einheit
- Akzeptierter Preis
- Steuerklassifikation oder berechneter Steuerkontext
- Operative Merkmale für Versand oder Dokumente

Die konkrete Implementierung bleibt privat. Die Architekturregel lautet, dass spätere Stammdatenänderungen historische Summen und Dokumente nicht still verändern dürfen.

## 8. Grenze zu externen Bestellungen

Marketplace-Daten gelangen als nicht vertrauenswürdige Transportdaten in MerchantFlow. Ein Adapter liest und überträgt die Payload, aber die Anwendung entscheidet über die Übernahme als lokalen Geschäftsdatensatz.

Die Annahme benötigt kontrollierte Entscheidungen zu:

- Identität des externen Quellsystems
- Duplikaterkennung
- Kundenzuordnung
- Adressnormalisierung
- Produktzuordnung
- Erforderlichen kaufmännischen Werten
- Initialem Lebenszykluszustand

Eine externe Payload wird nicht allein durch erfolgreichen Download verbindlich.

## 9. Bestelllebenszyklus

Das öffentliche Modell verwendet konzeptionelle Zustände und veröffentlicht nicht jeden produktiven Status oder Übergang.

Typische Kategorien sind:

- Entwurf oder importiert
- Validiert
- Akzeptiert oder gebucht
- In Erfüllung
- Abgeschlossen
- Storniert oder korrigiert

Übergänge sind Befehle mit Vorbedingungen. Ein Status darf nicht direkt durch UI-Code, Import-Mapping oder Dokumentdarstellung geändert werden.

## 10. Fachliche Invarianten

Repräsentative Invarianten sind:

- Eine Bestellung besitzt eine stabile interne Identität.
- Eine akzeptierte Bestellung enthält mindestens eine gültige Position.
- Historische Bestellwerte werden nicht durch Stammdatenänderungen überschrieben.
- Rechnungs- und Lieferrollen bleiben explizit.
- Die Identität einer externen Bestellung ist innerhalb ihrer Quelle eindeutig.
- Die Duplikatprüfung erfolgt vor Erstellung einer zweiten lokalen Transaktion.
- Dokumente werden nur für zulässige Bestellzustände erzeugt.
- Eine Stornierung bleibt auf die ursprüngliche Transaktion zurückführbar.
- Versanddaten werden vor Übergabe an externe Abläufe validiert.
- Eine geschützte Funktion benötigt die Freigabe der zentralen Funktionsentscheidung.
- Mehrdeutigkeit blockiert Änderungen, wenn Korrektheit nicht sicher festgestellt werden kann.

Dies sind Architekturbeispiele und keine vollständige Veröffentlichung des privaten Regelwerks.

## 11. Entscheidungsergebnisse

Domänen-Policies liefern strukturierte Ergebnisse statt boolescher Werte mit versteckter Bedeutung.

Konzeptionelle Ergebnisse sind:

- Vorgeschlagenen Vorgang akzeptieren
- Neuen Kunden oder neue Adressversion erstellen
- Bestehende geprüfte Zuordnung wiederverwenden
- Explizite Benutzerprüfung verlangen
- Ungültigen Übergang ablehnen
- Wegen mehrdeutiger Nachweise blockieren
- Wegen fehlender Funktion verweigern

Strukturierte Ergebnisse vereinheitlichen Oberfläche, Protokollierung, Tests und Transaktionsbehandlung, ohne Geschäftslogik in die Präsentation zu verlagern.

## 12. Zeitliche und Audit-Aspekte

Wichtige Datensätze können Metadaten zu Erstellung, Aktualisierung, Quelle und Version tragen. Zeitinformationen unterstützen die Nachvollziehbarkeit, ersetzen aber keine Domänenregeln.

Das Modell unterscheidet:

- Wann Daten empfangen wurden
- Wann sie akzeptierter Geschäftszustand wurden
- Welche Quelle sie geliefert hat
- Welche Version in einer Transaktion verwendet wurde
- Welcher kontrollierte Vorgang Dokument oder Export erzeugt hat

## 13. Servicegrenzen

Application Services koordinieren Aggregate über explizite Anwendungsfälle. Sie laden erforderliche Nachweise, rufen Domänen-Policies auf, wenden akzeptierte Pläne innerhalb einer Transaktion an und liefern ein strukturiertes Ergebnis.

Änderungen über Aggregatgrenzen hinweg erfolgen nicht durch uneingeschränkte Objektnavigation. Dadurch bleiben Transaktionsgrenzen sichtbar und unbeabsichtigte Kaskaden werden vermieden.

## 14. Auswirkungen auf Tests

Aus dem Domänenmodell ergeben sich fokussierte Tests für:

- Entscheidungstabellen zur Kundenzuordnung
- Erstellung und Erhalt von Adressversionen
- Snapshot-Verhalten von Bestellpositionen
- Vermeidung doppelter externer Bestellungen
- Gültige und ungültige Statusübergänge
- Berechtigung für Dokumente und Versand
- Fail-closed-Ergebnisse der Funktionsbewertung
- Erhalt historischer Daten nach Stammdatenänderungen

Die wichtigsten Invarianten können ohne JavaFX, SQLite und aktiven Marketplace getestet werden.

## 15. Öffentliche Abstraktionsgrenze

Dieses Dokument enthält bewusst nicht:

- Produktive Entity- und Package-Namen
- Vollständige Entscheidungstabellen und Bewertungsregeln
- Exakte Algorithmen für Dokumentnummern
- Kommerzielle Preis- oder Lizenzregeln
- Reale Schemas, Kennungen, Kundendaten und externe Payloads
- Details zu Manipulationsschutz und Signaturen

## 16. Verwandte Dokumentation

- [Systemarchitektur](03-system-architecture.de.md)
- [Datenbankdesign](05-database-design.de.md)
- [Konzeptionelles Domänendiagramm](../diagrams/domain-model.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
