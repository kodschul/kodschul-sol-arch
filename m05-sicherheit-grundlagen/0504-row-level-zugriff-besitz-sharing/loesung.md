# Lab 5.4 - Loesung: Row-Level-Zugriff, Besitz und Sharing bewerten

## Aufgabe 1: Eigentuemerstrategie

**Problem:**
Ein ausgeschiedener Nutzer wird in der Regel in Azure AD deaktiviert. Sein Dataverse-Systembenutzer-Datensatz wird auf "Disabled" gesetzt. Die 340 Kundenvertraege haben weiterhin diesen deaktivierten Nutzer als Eigentuemer. Das fuehrt zu:

- Kollegen koennen die Datensaetze nur sehen, wenn ihre Rolle BU- oder Org-Tiefe hat
- Kein aktiver Nutzer ist verantwortlich
- Workflows, die an den Eigentuemer erinnern oder eskalieren, schlagen fehl oder laufen ins Leere

**Empfohlene Team-Strategie:**
Teams als Eigentuemer fuer alle Produktivdaten. Empfohlen: Owner-Teams pro Regionseinheit (z.B. "Vertrieb-Nord", "Vertrieb-Sued"). Der Datensatz gehoert dem Team, nicht dem einzelnen Bearbeiter. Wer der aktuelle Bearbeiter ist, wird als Lookup-Feld "Zust\u00e4ndiger Mitarbeiter" erfasst - getrennt vom Eigentuemer.

**Migration der 340 Datensaetze:**

- Option 1: Bulk-Reassign ueber die Dataverse-Oberflaeche (Erweiterte Suche → Alle auswaehlen → Zuweisen an Team)
- Option 2: Power Automate Flow, der alle Datensaetze des deaktivierten Nutzers an ein Team uebertraegt
- Option 3: Dataverse Web API mit Batch-Request

---

## Aufgabe 2: Sharing-Szenario

**Empfehlung: Access Team per Power Automate Flow**

**Begruendung:**
Es handelt sich um eine Ausnahme-Situation (nicht jeder Vertriebsmitarbeiter soll alle Stoerungen sehen, nur die mit Bezug zu seinem Kunden). Das ist genau der Anwendungsfall fuer Sharing bzw. Access Teams.

**Umsetzung:**

1. Ein Power Automate Flow wird ausgeloest, wenn eine Stoerungsmeldung erstellt wird
2. Flow prueft: Gibt es einen Vertrag, der mit der betroffenen Klinik verknuepft ist?
3. Wenn ja: Flow liest den Eigentuemer/Zust\u00e4ndigen des Vertrags
4. Flow fuegt diesen Nutzer zum Access Team der Stoerungsmeldung hinzu (mit Read-Recht)

**Warum nicht Rollen:**
Um dem Vertriebsmitarbeiter alle Stoerungen seiner Kunden per Rolle zu gewaehren, braeuchte er Read mit einer Tiefe, die auch Stoerungen anderer Kunden einschliesst. Das waere entweder User (nur eigene - aber er besitzt keine Stoerungen) oder BU/Org (zu weit). Rollen koennen keinen datensatzbezogenen Filter "nur Stoerungen meiner Kunden" abbilden - dafuer ist Sharing/Access Teams das richtige Werkzeug.

---

## Aufgabe 3: Vertriebsleiter-Zugriff

| Kriterium                           | BU-Hierarchie + Parent:Child | Hierarchiesicherheit                  |
| ----------------------------------- | ---------------------------- | ------------------------------------- |
| Aufwand Einrichtung                 | Mittel (BU-Struktur pflegen) | Niedrig (Manager-Feld nutzen)         |
| Abhaengigkeit von AD                | Nein                         | Ja (Manager-Feld muss gepflegt sein)  |
| Funktioniert bei Umstrukturierungen | Immer (manuell anpassen)     | Nur wenn AD aktuell ist               |
| Mehrere Fuehrungsebenen             | Sauber abbildbar             | Begrenzte Konfiguration               |
| Transparenz                         | Hoch (BU-Zuweisung sichtbar) | Niedrig (implizit ueber Manager-Feld) |

**Empfehlung: BU-Hierarchie + Parent:Child**

Begruendung: In einem Energieversorger mit HR-Prozessen koennte das Manager-Feld in AD verzoegert aktualisiert sein (z.B. bei internen Wechseln). Wenn ein neuer Vertriebsleiter ernannt wird und das AD-Feld noch nicht gepflegt ist, sieht er keine Daten. Die BU-Hierarchie ist stabiler und transparenter. Der Mehraufwand fuer die BU-Pflege ist gering im Verhaeltnis zur Zuverlaessigkeit.

**Ausnahme:** Wenn die Organisation sehr flach ist (z.B. ein Vertriebsleiter, alle Mitarbeiter direkt darunter, keine weiteren Fuehrungsebenen) und die AD-Pflege zuverlaessig ist, ist Hierarchiesicherheit die einfachere Loesung.
