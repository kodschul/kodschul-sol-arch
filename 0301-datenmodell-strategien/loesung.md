# Loesung: Datenmodell-Strategien fuer Loesungen entwickeln

## Aufgabe 1: Entitaeten identifizieren

**Empfohlene Entitaeten und Begruendungen:**

| Entitaet | Als eigene Tabelle? | Begruendung |
|---|---|---|
| Lieferauftrag | Ja | Kernentitaet, wird mehrfach referenziert und hat eigene Felder |
| Kunde | Ja, Dataverse Account | Wird wiederverwendet, hat eigene Adress- und Kontaktfelder |
| Auftragsposition | Ja | 1:N zu Auftrag, variabel viele Positionen pro Auftrag |
| Fahrer | Ja, als SystemUser oder eigene Tabelle | Hat eigene Eigenschaften, wird in Berichten benoetigt |
| Notiz | Ja oder als Activity | Beliebig viele pro Auftrag, daher eigene Tabelle oder Dataverse Activity |

**Hinweis zur Wiederverwendung:** Dataverse stellt folgende Standardtabellen bereit:
- Account (Kunden, Lieferanten)
- Contact (Ansprechpartner)
- SystemUser (Mitarbeiter, Fahrer wenn Systemnutzer)

Kunden sollten als Account abgebildet werden, da Adressfelder bereits vorhanden sind und spaetere Integrationen (z.B. mit Dynamics) vereinfacht werden.

---

## Aufgabe 2: Beziehungen modellieren

**Korrektes ER-Modell:**

```
Account (Kunde)
    |
    | 1:N
    |
Lieferauftrag
    |          |
    | 1:N      | N:1
    |          |
Auftragsposition  SystemUser (Fahrer)

Lieferauftrag
    |
    | 1:N
    |
Notiz (oder Annotation/Activity)
```

**Beziehungsarten:**
- Auftrag zu Auftragsposition: 1:N (ein Auftrag hat viele Positionen)
- Auftrag zu Kunde: N:1 (viele Auftraege gehoeren einem Kunden, Lookup-Feld auf Lieferauftrag)
- Fahrer zu Auftraegen: N:1 (viele Auftraege koennen einem Fahrer zugewiesen sein)

Wenn ein Fahrer gleichzeitig mehreren Auftraegen zugeordnet sein kann und ein Auftrag mehrere Fahrer haben kann, waere eine N:N-Beziehung ueber eine Zwischentabelle notwendig. Im beschriebenen Szenario ist es jedoch wahrscheinlich ein einzelner Fahrer pro Auftrag, also N:1.

---

## Aufgabe 3: Strategie begruenden

**Empfohlene Strategie: Normalisiertes Modell**

Das normalisierte Modell ist die richtige Wahl, weil Kunden, Fahrer und Produktdaten an mehreren Stellen wiederverwendet werden. Wuerde man alle Informationen in einem flachen Modell speichern, muesste der Kundenname in jedem Auftrag erneut eingegeben werden. Eine Adressaenderung beim Kunden muesste in allen vergangenen Auftraegen nachgepflegt werden, was nicht nur aufwaendig sondern auch fehleranfaellig ist.

Das Event-Log-Modell waere uebertrieben, da keine Audit-Anforderungen an Zustandsveraenderungen bestehen. Das Erweiterungsmodell ist fuer dieses homogene Szenario nicht notwendig, da alle Auftraege dasselbe Datenprofil haben.

---

## Aufgabe 4: Risiken bewerten

**Analyse fuer den Bericht "Fahrer pro Monat":**

Damit dieser Bericht moeglich ist, muessen folgende Felder vorhanden sein:
- Fahrerzuordnung am Auftrag (Lookup auf SystemUser oder Fahrer-Tabelle)
- Tatsaechliches Lieferdatum (um Monat bestimmen zu koennen)
- Statusfeld (um "erledigt" von "offen" zu unterscheiden)

Alle drei sind im empfohlenen Modell vorhanden. Das tatsaechliche Lieferdatum wird als DateTime-Feld am Auftrag gespeichert. Mit einem Power BI Bericht oder einer Dataverse-Ansicht kann dann nach Monat gruppiert und nach Fahrer gefiltert werden.

**Flaches Modell versus normalisiertes Modell fuer den Bericht:**

Ein flaches Modell wuerde den Bericht erschweren, nicht unmoeglichen. Wenn der Fahrername als Freitext gespeichert wird, sind Tippfehler moeglich ("Maier" vs. "Meyer"). Fahrerdaten koennen nicht konsolidiert werden. Berichte per Fahrer waeren fehleranfaellig. Mit einem Lookup auf eine Fahrer-Tabelle hingegen ist der Bericht sauber und zuverlaessig.

**Fazit:** Das normalisierte Modell ist nicht nur fachlich sauber, es ist auch die einzige Grundlage, auf der ein belastbares Berichtswesen aufgebaut werden kann.
