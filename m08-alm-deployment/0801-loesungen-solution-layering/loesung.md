# Lab 8.1 - Loesung: Loesungen und Solution Layering sicher beherrschen

## Aufgabe 1: Solution-Segmentierungsstrategie

**Empfohlene Loesungsstruktur:**

| Loesung                | Inhalt                                               | Eigentuemer Team |
| ---------------------- | ---------------------------------------------------- | ---------------- |
| `[Projekt]_Foundation` | Tabellen, Spalten, Beziehungen, Alternativschluessel | Team A           |
| `[Projekt]_Security`   | Sicherheitsrollen, BU-Konfiguration                  | Team A           |
| `[Projekt]_Apps`       | Canvas Apps, Model-Driven Apps, Sitemaps             | Team B           |
| `[Projekt]_Automation` | Power Automate Flows, Plugins                        | Team C           |
| `[Projekt]_Config`     | Environment Variables, Connection References         | Alle / SA        |

**Deployment-Reihenfolge:**

1. Foundation (Tabellen zuerst - alles andere haengt davon ab)
2. Security (Rollen benoetigen Tabellen)
3. Config (Environment Variables und Connection References muessen vor Apps/Flows da sein)
4. Automation (Flows benoetigen Tabellen und Verbindungen)
5. Apps (Apps benoetigen Tabellen, ggf. Flows)

**Wie das die Probleme loest:**

_Problem 1 - Team B und C gleichzeitig:_
Jedes Team hat seine eigene Loesung. Team B aendert nur `Apps`, Team C nur `Automation`. Keine Konflikte mehr beim gleichzeitigen Export/Import.

_Problem 2 - Hotfix Flow ohne App-Aenderungen:_
Team C exportiert nur `Automation` und deployt nur diese Loesung in Prod. `Apps` bleibt auf dem alten Stand. Kein "alles oder nichts" mehr.

_Problem 3 - Sicherheitsrollen separat:_
`Security`-Loesung kann unabhaengig deployt werden, wenn sich Rollen aendern, ohne Apps oder Flows anfassen zu muessen.

---

## Aufgabe 2: Managed vs. Unmanaged

**Warum direkte Bearbeitung in Prod problematisch ist:**

_Technisch:_
In einer Managed Loesung sind Komponenten als "managed" markiert. Direkte Aenderungen in der Produktivumgebung erzeugen einen "unmanaged Layer" ueber dem managed Layer. Wenn spaeter eine neue Version der Managed Loesung importiert wird, koennen diese unmanaged Layer-Aenderungen ueberbleiben oder Konflikte erzeugen. Das ist der "Active Layer" im Solution-Layering und kann zu unerwartetem Verhalten fuehren.

_Architektonisch:_
Dev ist die Single Source of Truth. Wenn eine Aenderung zuerst in Prod gemacht wird, existiert sie nicht in Dev. Beim naechsten regulaeren Deployment wird sie ueberschrieben. Der Hotfix ist verloren.

**Korrekter Weg:**

1. Aenderung in Dev vornehmen
2. In die Loesung aufnehmen
3. Als Managed Loesung exportieren
4. In Test importieren und testen (Schnelltest, da Hotfix)
5. In Prod importieren

Bei echten Notfaellen: Schritte 4 (Test) kann uebersprungen werden mit dokumentierter Entscheidung.

**Ausnahmefall fuer direkte Aenderung in Prod:**
Umgebungsvariablen-Werte oder Connection-Reference-Zuweisungen - diese sind per Design umgebungsspezifisch und koennen/sollen direkt in der Zielumgebung gesetzt werden. Sie gehoeren nicht in die Loesung selbst.

---

## Aufgabe 3: Connection References

**Was eine Connection Reference ist:**
Eine Connection Reference ist ein Platzhalter in einer Loesung fuer eine externe Verbindung (SharePoint, Teams, SQL, etc.). Statt die Verbindung direkt im Flow zu speichern (was umgebungsspezifisch waere), zeigt der Flow auf die Connection Reference. In jeder Umgebung kann die Reference auf eine andere konkrete Verbindung zeigen.

**Was vor dem Import getan werden muss:**
In der Test-Umgebung muss eine SharePoint-Verbindung existieren, die mit `cr_SharePointMain` verknuepft werden kann. Das passiert entweder:

- Manuell nach dem Import: Im Solution-Import-Dialog wird die Reference mit einer bestehenden Verbindung verknuepft
- Automatisiert: Vor dem Import per pac CLI eine Verbindung anlegen und mit der Reference verknuepfen

**Automatisierung in einer Pipeline:**

1. `pac connection create` - Verbindung mit Service Principal anlegen
2. `pac solution import --connection-references` - Import mit Mapping-Datei, die Connection References auf konkrete Verbindungen mappt
3. Mapping-Datei (JSON) liegt im Repository und ist umgebungsspezifisch (eine fuer Test, eine fuer Prod)

```json
{
  "connectionReferences": {
    "cr_SharePointMain": {
      "connectionId": "/providers/Microsoft.PowerApps/connections/test-sharepoint-conn"
    }
  }
}
```

Damit wird kein manueller Schritt mehr benoetigt - die Pipeline mappt automatisch die richtige Verbindung in der Zielumgebung.
