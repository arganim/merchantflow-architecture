# Veröffentlichungsgrenzen und Sicherheitsregeln

## Zweck

Dieses Repository ist eine bereinigte Architektur-Fallstudie für Bewerbungen und das professionelle Portfolio. Es erklärt Architekturentscheidungen, Systemgrenzen, Arbeitsabläufe, Qualitätsmaßnahmen und den verantwortungsvollen Einsatz von KI bei der Entwicklung einer privaten Handelsplattform.

Es ist nicht das produktive Repository und enthält keine ausführbare Version der Anwendung.

## Verbindlicher Grundsatz

Jeder Inhalt, der in dieses Repository aufgenommen wird, muss für eine dauerhafte öffentliche Veröffentlichung geeignet sein.

Eine spätere Umstellung des Repositorys von öffentlich auf privat ist lediglich eine Änderung der zukünftigen Zugriffsrechte. Sie garantiert nicht, dass frühere öffentliche Kopien, Forks, Caches oder lokale Klone verschwinden. Informationen dürfen deshalb nicht veröffentlicht werden, wenn ihre dauerhafte Verfügbarkeit ein Datenschutz-, Sicherheits-, Rechts- oder Geschäftsrisiko darstellen würde.

## Erlaubte Inhalte

Folgende Inhalte dürfen nach einer Prüfung veröffentlicht werden:

- Eigenständig geschriebene Architekturerklärungen
- Vereinfachte Systemkontext- und Komponentendiagramme
- Abstrakte Domänenkonzepte und Beziehungen
- Verallgemeinerte Geschäftsabläufe und Statusübergänge
- Technologieentscheidungen und ihre technische Begründung
- Test-, Migrations-, CI- und Qualitätsstrategien
- Sicherheits- und Datenschutzgrundsätze
- Abstrahierte Architecture Decision Records
- Verantwortungsvolle KI-unterstützte Entwicklungsprozesse
- Synthetische Beispiele, die ausschließlich für dieses Repository erstellt wurden
- Öffentlich dokumentierte Eigenschaften der verwendeten Technologien

## Verbotene Inhalte

Folgende Inhalte dürfen niemals committed werden:

- Produktiver Quellcode oder kopierte Quellcodeausschnitte
- Dateien oder Git-Historie aus dem privaten Anwendungs-Repository
- Echte Daten von Kunden, Lieferanten, Mitarbeitern oder Geschäftspartnern
- Echte Namen, E-Mail-Adressen, Telefonnummern oder Postanschriften
- Echte Bestell-, Rechnungs-, Kunden-, Versand- oder Zahlungsnummern
- Datenbankdateien, Backups, Dumps, Migrationen oder produktive Schemata
- CSV-, PDF-, DOCX-, XLSX-, Log- oder Exportdateien aus der privaten Anwendung
- Screenshots mit echten oder internen Daten
- Produktive Dateipfade, Hostnamen, Domains oder Konto-Kennungen
- API-Schlüssel, Tokens, Passwörter, Secrets, Zertifikate oder private Schlüssel
- Lizenzdokumente, Signatur-Payloads, Passphrasen oder Schlüsselmaterial
- Environment-Dumps, sensible Kommandozeilen oder interne Stacktraces
- WooCommerce-, SMTP-, Zahlungs-, Versand- oder Drittanbieter-Zugangsdaten
- Umsätze, Kundenzahlen, Margen oder andere vertrauliche Kennzahlen
- Detaillierte Schutzmechanismen gegen Manipulation oder Lizenzumgehung
- Wirtschaftlich sensible Regeln, deren Offenlegung das Produkt schwächen könnte

## Standard für synthetische Daten

Beispiele müssen eindeutig fiktiv sein und dürfen nicht entstehen, indem echte Datensätze lediglich geringfügig verändert werden.

Zulässiges Beispiel:

```text
Kunde: C-10001
Name: Anna Beispiel
E-Mail: anna@example.test
Bestellung: ORD-2026-000001
```

Für fiktive E-Mail-Adressen und URLs wird die reservierte Domain `.test` verwendet. Beispielkennungen müssen ein neutrales Format besitzen, das nicht aus dem privaten System übernommen wurde.

## Abstraktionsregeln

Die Architekturdokumentation soll das technische Problem und die gewählte Lösungsklasse erklären, ohne sensible Implementierungsdetails zu reproduzieren.

Beispiele:

- Es darf erklärt werden, dass historische Bestelladressen vor späteren Änderungen der Kundenstammdaten geschützt werden.
- Das produktive Datenbankschema und interne Migrationsskripte dürfen nicht veröffentlicht werden.
- Es darf erklärt werden, dass Lizenzen mit asymmetrischer Signaturprüfung validiert werden.
- Schlüssel, vollständige Payload-Formate und Schutzdetails dürfen nicht veröffentlicht werden.
- Die Qualitätsstufen der CI-Pipeline dürfen beschrieben werden.
- Private Workflow-Dateien, Secrets, Logs und interne Repository-Namen dürfen nicht kopiert werden.

## Technische Trennung der Repositorys

Dieses Repository muss technisch vollständig vom privaten Anwendungs-Repository getrennt bleiben:

- Es darf nicht als Fork erstellt werden.
- Es darf die private Git-Historie nicht übernehmen.
- Es darf das private Repository nicht als Submodule einbinden.
- Es darf keine privaten Commit-Hashes, Branch-Namen oder Pull-Request-Links veröffentlichen.
- Dokumente müssen neu geschrieben und bereinigt werden, statt interne Texte unverändert zu kopieren.

## Prüfung vor der Veröffentlichung

Vor der Umstellung auf öffentlich muss geprüft werden:

- [ ] Jede committed Datei wird tatsächlich für das Portfolio benötigt.
- [ ] Alle Personen, Unternehmen, Anschriften und Kennungen sind fiktiv.
- [ ] Es sind keine Secrets, Zugangsdaten, Schlüssel oder sensiblen Konfigurationen enthalten.
- [ ] Es sind keine echten Datenbanken, Exporte, Dokumente, Screenshots oder Logs enthalten.
- [ ] Es sind keine Produktionspfade, privaten URLs, internen Repository-Links oder Kontonamen enthalten.
- [ ] Alle Branches, Tags, Commits und gelöschten Dateien in der Historie wurden geprüft.
- [ ] Diagramme enthalten ausschließlich freigegebene Abstraktionen.
- [ ] Aussagen zu Technologien und Qualität sind korrekt und eindeutig eingegrenzt.
- [ ] Das Repository erklärt deutlich, dass der produktive Quellcode privat bleibt.
- [ ] Vor der Veröffentlichung wurde eine abschließende manuelle Prüfung durchgeführt.

## Vorgehen bei einem Vorfall

Wenn versehentlich sensible Informationen committed werden, muss die Veröffentlichung sofort gestoppt werden. Das Löschen in einem späteren Commit reicht nicht aus, weil die Informationen weiterhin in der Git-Historie enthalten sein können. Betroffene Zugangsdaten müssen gesperrt oder ausgetauscht und die Repository-Historie muss geprüft werden, bevor die Veröffentlichung fortgesetzt werden darf.

## Urheberrecht

Copyright © 2026 Mohamed Abergna. Alle Rechte vorbehalten.

Dieses Repository wird als professionelles Portfolio und Architektur-Fallstudie bereitgestellt. Eine Open-Source-Lizenz wird nur dann eingeräumt, wenn eine gesonderte Lizenzdatei dies ausdrücklich festlegt.
