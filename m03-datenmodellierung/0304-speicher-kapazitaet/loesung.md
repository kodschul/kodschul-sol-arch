# Loesung: Speicher- und Kapazitaetsauswirkungen architektonisch beruecksichtigen

## Aufgabe 1: Speicherbedarf abschaetzen

**Database Storage pro Jahr:**
- 80.000 Datensaetze x 5 KB (Basisfelder) = 400 MB
- 80.000 Beschreibungstexte x 50 KB = 4.000 MB = 4 GB
- Summe Database pro Jahr: ca. 4,4 GB
- Summe fuer 10 Jahre: ca. 44 GB

**File Storage pro Jahr:**
- 80.000 Faelle x 8 Fotos x 3 MB = 1.920.000 MB = ca. 1,88 TB
- Summe fuer 10 Jahre: ca. 18,8 TB

**Log Storage pro Jahr:**
- 80.000 Faelle x 12 Statusaenderungen x 2 KB = 1.920.000 KB = ca. 1,88 GB
- Summe fuer 10 Jahre: ca. 18,8 GB

**Gesamtuebersicht:**
| Kategorie | Pro Jahr | 10 Jahre |
|---|---|---|
| Database | ca. 4,4 GB | ca. 44 GB |
| File | ca. 1,88 TB | ca. 18,8 TB |
| Log | ca. 1,88 GB | ca. 18,8 GB |

---

## Aufgabe 2: Kontingent pruefen

**Verfuegbares Kontingent (200 Nutzer x Power Apps Premium):**
- Database: 200 x 250 MB = 50.000 MB = ca. 48,8 GB
- File: 200 x 2 GB = 400 GB
- Log: 200 x 2 GB = 400 GB

**Vergleich:**
| Kategorie | Verfuegbar | Benoetigt (10 Jahre) | Ausreichend? |
|---|---|---|---|
| Database | 48,8 GB | 44 GB | Knapp ausreichend |
| File | 400 GB | 18.800 GB (18,8 TB) | Nein, Faktor 47 zu wenig |
| Log | 400 GB | 18,8 GB | Ja, ausreichend |

**Fazit:** Der File Storage ist der kritische Engpass. Die Fotos verursachen einen Bedarf, der 47-mal ueber dem Kontingent liegt.

---

## Aufgabe 3: Loesungsstrategien

**Strategie 1: Fotos nach Azure Blob Storage auslagern**
- Aenderung: Fotos werden nicht in Dataverse gespeichert, sondern in Azure Blob Storage
- Datenmodell: URL-Feld je Foto auf dem Schadensdatensatz, oder eigene Tabelle "SchadensFoto" mit URL-Feldern
- Vorteil: Kein Dataverse File Storage verbraucht, Azure Blob ist sehr guenstig (ca. 0,02 USD pro GB/Monat)
- Neue Komplexitaet: Power Automate Flow benoetigt, der Foto-Upload nach Azure weiterleitet und URL zurueckschreibt; Zugriffsrechte muessen separat verwaltet werden

**Strategie 2: SharePoint Integration**
- Aenderung: Fotos werden in einer SharePoint-Dokumentbibliothek gespeichert, pro Schadensfall ein SharePoint-Ordner
- Datenmodell: Spalte "SharePoint-Link" am Schadensdatensatz
- Vorteil: SharePoint ist in M365 enthalten, volle Dokumentenversionierung, bekannte Nutzeroberflaeche
- Neue Komplexitaet: Loeschen muss koordiniert werden, kein nativer Dataverse-Upload-Button, erfordert Custom-UI oder Flow

**Optionale Strategie 3: Bilddaten komprimieren**
- Vor dem Upload auf Mobilgeraet oder Web-App: Fotos auf 800 KB komprimieren
- Reduktion: Von 3 MB auf 0,8 MB = Faktor 3,75 Reduktion
- Auch nach Komprimierung: 18,8 TB / 3,75 = ca. 5 TB, immer noch weit ueber dem Kontingent
- Als alleinige Massnahme nicht ausreichend, aber als Ergaenzung sinnvoll

---

## Aufgabe 4: Audit-Anforderung praezisieren

**Variante A: Dataverse-eigener Audit-Mechanismus**
- Vorteile: Keine Eigenentwicklung, System protokolliert automatisch, Audit-Viewer im Admin Center vorhanden
- Nachteile: Benoetigt Log Storage, schwer abzufragen per API, Audit-Daten koennen nicht direkt in Power BI visualisiert werden, schwieriger zu archivieren

**Variante B: Eigene Status-Historie-Tabelle**
- Jede Statusaenderung wird als eigener Datensatz in einer Tabelle "SchadensStatusHistorie" gespeichert (Felder: SchadensId, AlterStatus, NeuerStatus, GeaendertAm, GeaendertVon)
- Ausgeloest per Power Automate Flow oder Plug-In
- Vorteile: Voll abfragbar per View, FetchXML, Power BI; kann in Power Apps angezeigt werden; volle Kontrolle ueber Inhalt und Darstellung; kein Log Storage verbraucht
- Nachteile: Eigenentwicklung notwendig, Flow oder Plugin muss gebaut und gewartet werden, kein "offizieller" Audit-Trail (je nach Compliance-Anforderung moeglicherweise nicht akzeptiert)

**Empfehlung:** Variante B ist technisch flexibler und guenstiger im Speicher. Ob sie complianceseitig akzeptiert wird, haengt von den konkreten Pruefanforderungen ab. Das sollte mit dem Compliance-Beauftragten des Unternehmens geklaert werden, bevor die technische Entscheidung faellt.
