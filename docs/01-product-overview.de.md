# Produktübersicht

## 1. Zusammenfassung

MerchantFlow ist eine Desktop-First-Handelsplattform, die Kunden-, Produkt-, Bestell-, Dokument-, Versand-, Buchhaltungs- und Marketplace-Prozesse in einer kontrollierten Arbeitsumgebung zusammenführt.

Die Plattform löst ein verbreitetes Problem kleiner und mittlerer Händler: Wichtige Geschäftsdaten liegen häufig verteilt in Onlineshops, Tabellen, Exportdateien, Dokumentordnern, E-Mail-Programmen und manuell gepflegten Aufzeichnungen. Diese Zersplitterung führt zu doppelter Arbeit, widersprüchlichen Daten, geringer Nachvollziehbarkeit und einem erhöhten Risiko operativer Fehler.

MerchantFlow schafft einen einheitlichen Arbeitsablauf und lässt gleichzeitig die Kontrolle über geschäftskritische Daten und Entscheidungen beim Händler.

Dieses Dokument beschreibt das Produktkonzept bewusst auf einer abstrahierten Ebene. Produktiver Quellcode, Kundendaten, Datenbankdefinitionen, Zugangsdaten und wirtschaftlich sensible Regeln werden nicht offengelegt.

## 2. Produktvision

Die langfristige Vision ist eine zuverlässige Plattform für Handelsprozesse, die sich von einer internen Desktoplösung zu einem konfigurierbaren Produkt für Händler weiterentwickeln lässt.

Das Produkt soll:

- wiederkehrende Verwaltungsarbeit reduzieren
- eine einheitliche Sicht auf Kunden und Bestellungen schaffen
- historische Geschäftsunterlagen bewahren
- operative Entscheidungen nachvollziehbar machen
- Offline- und Local-First-Arbeitsabläufe unterstützen
- externe Kanäle anbinden, ohne die Kontrolle über die Domäne abzugeben
- unterschiedliche Produktausgaben kontrolliert bereitstellen
- trotz neuer Anforderungen und Integrationen wartbar bleiben

## 3. Zielgruppen und Benutzerrollen

Die primären Benutzer sind Händler und operative Mitarbeiter, die Bestellungen bearbeiten, Kunden und Produkte verwalten, Geschäftsdokumente erstellen, Versanddaten vorbereiten und buchhaltungsorientierte Informationen prüfen.

Typische Rollen sind:

| Rolle | Hauptanforderungen |
| --- | --- |
| Geschäftsinhaber | Überblick, Kontrolle, Nachvollziehbarkeit und zuverlässige Unterlagen |
| Auftragsbearbeitung | Schnelle Prüfung, Buchung, Dokumenterstellung und Korrektur von Bestellungen |
| Kundenservice | Verlässliche Kundenhistorie sowie Kontakt- und Adressverwaltung |
| Versand | Korrekte Versanddaten, Tagesexporte und verfügbare Dokumente |
| Buchhaltung | Vollständige Rechnungsunterlagen, steuerbezogene Übersichten und strukturierte Exporte |
| Administration | Konfiguration, Sicherung, Diagnosen, Integrationen und Lizenzstatus |

In einem kleineren Unternehmen kann eine Person mehrere dieser Rollen übernehmen.

## 4. Produktgrenzen

MerchantFlow führt die lokale operative Geschäftsakte. Externe Systeme können Bestellungen liefern oder ausgewählte Ausgaben erhalten, ersetzen aber nicht die Domänenregeln der Anwendung.

### Innerhalb der Produktgrenze

- Kunden- und Adressdaten
- Produktstammdaten
- Bestellverarbeitung und Bestellstatus
- Erstellung von Geschäftsdokumenten
- Versandvorbereitung und Export
- Buchhaltungsorientierte Berichte
- Konfiguration und Diagnosen
- Steuerung von Integrationen
- Bewertung von Editionen und Berechtigungen

### Außerhalb der Produktgrenze

- Betrieb des Marketplace-Shops
- Zahlungsabwicklung durch Zahlungsanbieter
- Physische Zustellung durch Versanddienstleister
- Infrastruktur für die E-Mail-Zustellung
- Offizielle Steuererklärungen
- Externe Identitäts- oder Zugriffsverwaltung

Diese externen Verantwortlichkeiten können über kontrollierte Adapter angebunden werden, ohne Bestandteil der Kerndomäne zu werden.

## 5. Zentrale Funktionsbereiche

### 5.1 Kundenverwaltung

Die Kundenverwaltung stellt durchsuchbare Kundendatensätze, Kontaktdaten, Herkunft, Aktivitätsstatus, Notizen, Adresshistorie und Verknüpfungen zu relevanten Bestellungen bereit.

Die Architektur berücksichtigt, dass sich Name, E-Mail-Adresse, Telefonnummer und Postanschrift eines Menschen ändern können. Die Identitätsauflösung benötigt deshalb explizite Regeln und darf mehrdeutige Datensätze nicht still zusammenführen.

### 5.2 Adressverwaltung

Adressen besitzen sowohl eine operative als auch eine historische Bedeutung. Eine aktuelle Kundenadresse kann korrigiert oder ersetzt werden. Eine Adresse, die bereits zu einer abgeschlossenen Bestellung gehört, muss dagegen möglicherweise für Dokumentation und Nachvollziehbarkeit unverändert bleiben.

Die Plattform unterscheidet deshalb zwischen Kundenstammadressen und geschützten bestellbezogenen Adress-Snapshots oder Versionen.

### 5.3 Produktverwaltung

Die Produktverwaltung pflegt kaufmännische und operative Informationen wie Kennungen, Beschreibungen, Preise, steuerbezogene Merkmale, Gewichte, Ursprungsinformationen, Versanddaten und Aktivitätsstatus.

Validierungen verhindern, dass unvollständige Angaben unbemerkt in Dokument-, Zoll- oder Versandprozesse gelangen.

### 5.4 Bestellverwaltung

Bestellungen können manuell oder über externe Verkaufskanäle entstehen. Unabhängig von ihrer Herkunft durchlaufen sie einen kontrollierten Prozess, der Kundendaten, Adressen, Produkte, Preise, Steuerverhalten, Versand, Zahlungsinformationen und Dokumentbereitschaft prüft.

Statusübergänge sind explizit. Aktionen wie Buchung oder Stornierung müssen definierte Voraussetzungen erfüllen und nachvollziehbare Ergebnisse erzeugen.

### 5.5 Dokumenterstellung

Die Plattform erzeugt Geschäftsdokumente wie Rechnungen, Lieferscheine und Stornodokumente aus geprüften Bestelldaten.

Diese Dokumente bilden historische Geschäftsvorgänge ab. Regeln für Erstellung und erneute Erstellung müssen deshalb verhindern, dass spätere Änderungen an Stammdaten abgeschlossene Transaktionen unbemerkt verändern.

### 5.6 Versandprozesse

Die Versandfunktionen bereiten tägliche Sendungsdaten und strukturierte Exporte für Versanddienstleister vor. Validierung, Duplikaterkennung, Formatregeln und Zeichencodierung sind Bestandteil eines kontrollierten Exportprozesses.

Die Plattform bereitet Versanddaten vor, führt aber nicht den physischen Transport aus.

### 5.7 Buchhaltungsorientierte Auswertungen

Die Buchhaltungsfunktionen stellen strukturierte Ansichten und Exporte aus gebuchten Geschäftsvorgängen bereit. Dazu können Rechnungsbücher, Periodenübersichten, Produktmengen, steuerbezogene Ansichten sowie Monats- und Jahresberichte gehören.

Die Plattform unterstützt die operative Buchhaltungsvorbereitung. Sie ersetzt weder professionelle Steuerberatung noch offizielle Systeme zur Steuerübermittlung.

### 5.8 Marketplace-Integration

Marketplace-Connectoren rufen externe Bestelldaten über Adapter ab. Importierte Daten werden normalisiert und validiert, bevor sie Teil der lokalen Geschäftsakte werden.

Externe Daten gelten nicht automatisch als vertrauenswürdig. Mehrdeutige Kundenzuordnungen, unvollständige Produktinformationen, ungültige Adressen und unzulässige Statusübergänge müssen gemäß expliziten Regeln angezeigt oder blockiert werden.

### 5.9 Konfiguration, Diagnosen und Sicherung

Administrative Funktionen verwalten Unternehmensdaten, Steuer- und Dokumenteinstellungen, Sprache, E-Mail, Speicher, Versand, Integrationen, Sicherungsverhalten und Laufzeitdiagnosen.

Diagnosen sollen die aktive Umgebung und den verwendeten Speicherort verständlich machen, ohne unnötig sensible Werte offenzulegen.

### 5.10 Editionen und Lizenzierung

Die Plattform kann abhängig von der lizenzierten Edition unterschiedliche Funktionen freigeben. Funktionsentscheidungen werden zentral bewertet, damit Geschäftsservices und Benutzeroberflächen denselben Regeln folgen.

Unsichere oder ungültige sicherheitsrelevante Lizenzzustände folgen dem Fail-closed-Prinzip. Diese Fallstudie beschreibt nur das Architekturprinzip und veröffentlicht weder sensibles Lizenzmaterial noch Details zu Schutzmaßnahmen gegen Umgehung.

## 6. Zentrale fachliche Invarianten

Das Produkt basiert auf mehreren verbindlichen Regeln:

1. Historische Bestellungen dürfen durch spätere Änderungen der Kundenstammdaten nicht still verändert werden.
2. Mehrdeutige Kundenidentitäten dürfen nicht automatisch zusammengeführt werden.
3. Ungültige Statusübergänge müssen blockiert werden.
4. Geschäftsdokumente dürfen nur aus geprüften und zuordenbaren Daten entstehen.
5. Datenbankänderungen müssen bestehende Installationen und Geschäftsdaten erhalten.
6. Externe Daten müssen lokale Validierungen durchlaufen, bevor sie als verbindlich gelten.
7. Sicherheitsrelevante Unsicherheit darf geschützte Funktionen nicht freigeben.
8. Fehlgeschlagene Aktionen dürfen keine teilweise übernommenen Geschäftsänderungen hinterlassen.

## 7. Qualitätsmerkmale

### Zuverlässigkeit

Geschäftsvorgänge müssen entweder vollständig gemäß ihrem Vertrag ausgeführt werden oder ohne widersprüchlichen Zwischenzustand fehlschlagen.

### Datenintegrität

Beziehungen zwischen Kunden, Adressen, Bestellungen und Dokumenten müssen auch nach späteren Änderungen und Migrationen nachvollziehbar bleiben.

### Wartbarkeit

Benutzeroberfläche, Application Services, Domänenregeln, Persistenz und externe Adapter sollen klar getrennte Verantwortlichkeiten und testbare Grenzen besitzen.

### Nachvollziehbarkeit

Wichtige Statusänderungen und blockierte Entscheidungen sollen durch strukturierte Zustände, Diagnosen und auditierbare Metadaten verständlich bleiben.

### Datenschutz und Sicherheit

Personenbezogene Daten müssen innerhalb kontrollierter operativer Grenzen bleiben. Secrets, Lizenzmaterial und sensible Konfigurationen dürfen weder über Dokumentation noch über Logs oder Diagnosen offengelegt werden.

### Benutzerfreundlichkeit

Komplexe Geschäftsregeln sollen durch klare Abläufe, verständliche Validierungsmeldungen und Oberflächen dargestellt werden, die auch bei realistischen Fenstergrößen nutzbar bleiben.

### Erweiterbarkeit

Neue Connectoren, Produktausgaben, Dokumentregeln und Geschäftsfunktionen sollen ergänzt werden können, ohne bestehende Domänenverträge zu schwächen.

## 8. Entwicklungsansatz

Die Entwicklung erfolgt in kleinen, prüfbaren Phasen. Jede Phase definiert vor ihrer endgültigen Abnahme den Umfang, die Grenzen, Akzeptanzkriterien, betroffenen Verträge, fokussierten Tests und Anforderungen an die Gesamtregression.

KI kann bei Lösungsalternativen, Implementierung, Refactoring, Tests, Diagnosen und Dokumentation unterstützen. Anforderungen, Architekturentscheidungen, Sicherheitsgrenzen, Reviews und finale Freigabe bleiben in menschlicher Verantwortung.

## 9. Stand des Portfolios

Dieses öffentliche Repository dokumentiert die Architektur schrittweise. Geplante Themen sind:

- Begründung der Technologieentscheidungen
- System- und Komponentenarchitektur
- Domänenmodell
- Datenbankentwicklung
- Bestelllebenszyklus
- Kundenidentität und Adressversionierung
- Lizenz- und Funktionsarchitektur
- CI- und Teststrategie
- Sicherheit und Datenschutz
- KI-unterstützter Engineering-Prozess
- Architecture Decision Records

Alle zukünftigen Dokumente unterliegen den Veröffentlichungsgrenzen dieses Repositorys.
