# Kunden- und Adressversionierung

## 1. Zweck und Umfang

Dieses Dokument erklärt, wie MerchantFlow Kundenidentität, veränderbare Kundenstammdaten, Adressversionen und den historischen Adresskontext einer Bestellung trennt.

Das Design behandelt ein typisches Handelsproblem: Menschen ziehen um, Kontaktdaten ändern sich, externe Kanäle liefern unterschiedliche Schreibweisen und historische Bestellungen müssen trotzdem korrekt und zuordenbar bleiben.

Produktive Matching-Regeln, Bewertungsschwellen, Schemas, personenbezogene Daten und kundenspezifische Ausnahmen sind bewusst ausgeschlossen.

## 2. Designziele

Das Kunden- und Adressmodell unterstützt:

- Stabile interne Kundenidentität
- Explizite Nachweise für Zuordnungen
- Sichere Behandlung mehrdeutiger Datensätze
- Erhaltene Adresshistorie
- Unabhängige Rechnungs- und Lieferrollen
- Unveränderlichen oder geschützten Bestelladresskontext
- Kontrollierte Synchronisierung mit externen Quellen
- Nachvollziehbare Korrekturen
- Datenschutzbewusste Diagnosen und Tests

## 3. Zentrale Unterscheidungen

| Konzept | Veränderbar? | Historische Verantwortung |
| --- | --- | --- |
| Kundenidentität | Stabil | Verbindet die langfristige Geschäftsbeziehung |
| Kundenkontaktdaten | Ja, durch kontrollierte Änderungen | Frühere Werte bleiben bei Bedarf zuordenbar |
| Kundenadressversion | Neue Versionen können entstehen | Frühere Versionen werden erhalten |
| Bestelladresse | Nach Annahme der Transaktion geschützt | Hält den Rechnungs- oder Lieferkontext dieser Bestellung fest |
| Externe Kundenreferenz | Quellenabhängig | Unterstützt Synchronisierung, ersetzt aber nicht die lokale Identität |

Die zentrale Regel lautet, dass aktuelle Stammdaten und historische Transaktionsdaten unterschiedliche Zwecke erfüllen und einander nicht überschreiben dürfen.

## 4. Kundenidentität

Ein Kunde besitzt eine stabile interne Identität, die unabhängig von veränderbaren Namen, E-Mail-Adressen, Telefonnummern und Postanschriften ist.

Geschäftsmerkmale unterstützen Suche und Zuordnung, gelten aber ohne explizite Domänenregel nicht als global eindeutig.

Beispiele:

- Zwei verschiedene Personen können denselben Namen tragen.
- Ein Haushalt kann dieselbe Adresse verwenden.
- Eine E-Mail-Adresse kann sich ändern oder wiederverwendet werden.
- Eine Organisation kann mehrere Kontakte und Lieferorte besitzen.
- Externe Kanäle können dieselben Daten unterschiedlich formatieren.

## 5. Identitätsnachweise

Die Kundenzuordnung bewertet eine Menge von Nachweisen und nicht nur ein bequemes Feld.

Konzeptionelle Nachweiskategorien sind:

- Geprüfte interne oder quellenbezogene Referenz
- Normalisierte Übereinstimmung von E-Mail oder Telefon
- Namens- und Organisationskontext
- Adress- und Länderkontext
- Frühere akzeptierte Beziehung
- Widersprüchliche Nachweise

Stärke und Konflikte der Nachweise werden explizit bewertet. Die vollständige private Entscheidungstabelle wird nicht veröffentlicht.

## 6. Normalisierungsgrenze

Normalisierung erzeugt vergleichbare Werte, entscheidet aber nicht über Identität.

Beispiele sind:

- Normalisierung von Leerzeichen und Groß-/Kleinschreibung
- Unicode-bewusste Textbehandlung
- Länder- und Postformatierung
- Vergleichsdarstellung von Telefonnummern
- Vergleichsdarstellung von E-Mail-Adressen
- Trennung von Personen-, Organisations- und Adressfeldern

Für Geschäftsvorgänge benötigte Originalwerte bleiben verfügbar. Eine normalisierte Übereinstimmung ist ein unterstützender Nachweis und keine automatische Berechtigung zur Zusammenführung.

## 7. Auflösungsergebnisse

Die Customer-Resolution-Policy liefert strukturierte Ergebnisse:

- Geprüfter vorhandener Kunde
- Neuer Kunde erforderlich
- Vorhandener Kunde mit neuen Kontakt- oder Adressdaten
- Prüfung erforderlich, weil Nachweise unvollständig sind
- Blockiert, weil Kandidaten widersprüchlich sind

Der Aufrufer erhält Ergebnis und Erklärungskategorie. Er erhält keine Berechtigung, still einen bequemen Kandidaten auszuwählen.

## 8. Mehrdeutigkeits-Policy

MerchantFlow folgt bei mehrdeutiger Identität dem Fail-closed-Prinzip.

Wenn die verfügbaren Nachweise keine sichere Kundenbindung erlauben, führt der Ablauf Folgendes aus:

- Vorhandenen Kunden nicht verändern
- Bestellung nicht dem ersten Suchergebnis zuordnen
- Kandidaten nicht automatisch zusammenführen
- Prüf- oder Blockierungsergebnis liefern
- Ausreichenden nicht sensiblen Diagnosekontext zur Erklärung bewahren

Auch die Erstellung eines neuen Kunden kann blockiert werden, wenn dadurch ein Konflikt mit einer starken externen Identität entstünde.

## 9. Adressverantwortung

Eine Kundenadressversion gehört zum Kundenaggregat. Sie repräsentiert einen bekannten Ort in einem Zeitraum der Geschäftsbeziehung.

Eine Adressversion kann konzeptionell enthalten:

- Adressrolle oder Verwendungszweck
- Normalisierte Vergleichsform
- Ursprünglich akzeptierte Darstellung
- Herkunfts- und Erstellungsmetadaten
- Aktiven oder ersetzten Zustand

Die konkrete Datenbankdarstellung bleibt privat.

## 10. Entscheidung über Adressversionen

Bei neuen Adresseingaben vergleicht die Policy diese mit den kundenbezogenen Versionen.

Konzeptionelle Ergebnisse sind:

- Geprüfte gleichwertige Version wiederverwenden
- Neue Adressversion erstellen
- Getrennte Rechnungs- und Lieferversionen bewahren
- Unvollständige oder ungültige Eingabe ablehnen
- Prüfung verlangen, wenn die Zuordnung mehrdeutig ist

Eine Adressänderung bedeutet Auswahl oder Erstellung einer geeigneten Version und nicht das Umschreiben jedes historischen Vorkommens.

## 11. Bestellungsbezogener Adresskontext

Eine akzeptierte Bestellung bewahrt die für ihre Transaktion verwendeten Rechnungs- und Lieferwerte. Dieser Kontext gehört zur Bestellhistorie.

Der Bestelladressdatensatz schützt:

- Adressrolle
- Akzeptierte Darstellung
- Beziehung zur Bestellung
- Zeitbezogenen Geschäftskontext
- Zuordnung für spätere Dokumente und Versandausgaben

Spätere Änderungen an Kundenstammdaten verändern diesen Datensatz nicht still.

## 12. Rechnungs- und Lieferrollen

Rechnungs- und Lieferadressen werden unabhängig bewertet.

Sie können sein:

- Derselbe akzeptierte Ort
- Unterschiedliche kundenbezogene Versionen
- Einmaliges Transaktionsziel, soweit die Policy dies erlaubt
- Unvollständig oder widersprüchlich und deshalb blockiert

Die Rolle bleibt explizit, damit Dokumente und Erfüllung die Absicht nicht aus Feldgleichheit ableiten.

## 13. Eingaben externer Kanäle

Externe Kanäle können Kunden- und Adressdaten liefern, kontrollieren aber nicht die lokale Identität.

Die Integrationsgrenze:

1. Bewahrt quellenbezogene Referenzen.
2. Überträgt Felder in einen internen Resolution Request.
3. Normalisiert Vergleichswerte.
4. Bewertet Kundennachweise.
5. Bewertet Rechnungs- und Lieferadressen.
6. Erstellt einen akzeptierten Plan oder liefert Prüfung beziehungsweise Blockierung.

Die Struktur roher Payloads gelangt nicht in das Domänenmodell.

## 14. Planung vor Mutation

Die Auflösung liefert einen Plan, bevor Kunden- oder Adressdatensätze verändert werden.

Der Plan kann beschreiben:

- Vorhandenen Kunden zur Wiederverwendung
- Neu zu erstellenden Kunden
- Nach Policy zu ändernde Kontaktwerte
- Wiederzuverwendende oder neue Adressversionen
- Zu bewahrende Transaktionsadresswerte
- Konflikte, die Mutation verhindern

Der Application Service wendet nur einen akzeptierten Plan innerhalb der Bestelltransaktion an.

## 15. Korrekturen

Korrekturen sind explizite Vorgänge mit anderer Bedeutung als gewöhnliche Stammdatenänderungen.

Eine Korrektur kann erfordern:

- Benutzerabsicht und Grund
- Berechtigung anhand des Bestellzustands
- Neue Adress- oder Dokumentversion
- Erhalt des ursprünglichen Datensatzes
- Auditmetadaten
- Regeln zur Neuerzeugung betroffener Ausgaben

Abgeschlossene Historie wird nicht über eine allgemeine Kundenbearbeitungsmaske umgeschrieben.

## 16. Zusammenführung und Trennung von Kunden

Das Zusammenführen von Kundendatensätzen ist risikoreich, weil es die Verantwortung für Bestellungen, Adressen und Dokumente verändern kann.

Soweit unterstützt, werden Zusammenführung oder Trennung als eigener geprüfter Anwendungsfall behandelt mit:

- Expliziter Quell- und Zielidentität
- Konflikterkennung
- Vorschau betroffener Beziehungen
- Transaktionaler Ausführung
- Nachvollziehbarem Ergebnis

Diese öffentliche Fallstudie enthält keine produktiven Zusammenführungsregeln.

## 17. Nebenläufigkeit und Duplikatvermeidung

Gleichzeitiger Import und manuelle Arbeit können denselben Kandidaten sehen, bevor eine Transaktion committed.

Schutz kann umfassen:

- Erneute Prüfung starker Identität vor Commit
- Quellenbezogene Eindeutigkeitsbedingungen
- Kurze Transaktionen
- Explizite Konfliktergebnisse
- Wiederholung der vollständigen Resolution-Policy

Wiederholung überspringt niemals Identitäts- oder Adressvalidierung.

## 18. Deaktivierung und Aufbewahrung

Die Deaktivierung eines Kunden oder einer Adressversion verhindert ungeeignete zukünftige Nutzung, löscht aber keine historischen Bestellungen.

Aufbewahrung und datenschutzbedingte Löschung sind getrennte kontrollierte Policies. Geschäftliche Aufbewahrung, rechtliche Pflichten und Betroffenenrechte müssen explizit abgewogen werden und dürfen nicht als uneingeschränkte Kaskadenlöschung umgesetzt sein.

## 19. Diagnosen und Datenschutz

Diagnosen zur Auflösung müssen Ergebnisse erklären, ohne unnötige personenbezogene Daten offenzulegen.

Sichere Diagnosekategorien können angeben, dass:

- Eine starke Referenz übereinstimmte
- Mehrere Kandidaten widersprüchlich waren
- Erforderliche Nachweise fehlten
- Eine neue Version erstellt wurde
- Eine historische Bindung erhalten blieb

Für nützliche Protokolle sind keine vollständigen Adressen, externen Payloads oder Secrets erforderlich.

## 20. Teststrategie

Repräsentative Tests umfassen:

- Exakte geprüfte Identitätsübereinstimmung
- Ähnliche Namen bei verschiedenen Kunden
- Geänderte E-Mail- oder Telefondaten
- Derselbe Kunde mit neuer Adresse
- Getrennte Rechnungs- und Lieferadressen
- Wiederverwendung einer gleichwertigen normalisierten Adresse
- Erhalt historischer Bestelladressen
- Mehrdeutige Kandidaten und Fail-closed-Verhalten
- Gleichzeitige Duplikatversuche
- Kontrollierte Korrektur und Deaktivierung

Für öffentliche Beispiele und automatisierte Tests werden synthetische Daten verwendet.

## 21. Öffentliche Abstraktionsgrenze

Dieses Dokument enthält bewusst nicht:

- Produktive Matching-Gewichte und Schwellwerte
- Vollständige Zusammenführungs- und Korrekturregeln
- Reale Kunden- oder Adressbeispiele
- Exakte Datenbank-Constraints und Abfragelogik
- Marketplace-Kontokennungen und Payloads
- Aufbewahrungsfristen eines bestimmten Unternehmens oder Rechtsraums

## 22. Verwandte Dokumentation

- [Domänenmodell](04-domain-model.de.md)
- [Datenbankdesign](05-database-design.de.md)
- [Bestellablauf](06-order-workflow.de.md)
- [Diagramm zur Kundenzuordnung](../diagrams/customer-flow.de.md)
- [Veröffentlichungsgrenzen](../PUBLICATION-SCOPE.de.md)
