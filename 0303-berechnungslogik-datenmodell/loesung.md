# Loesung: Berechnungslogik im Datenmodell korrekt bewerten

## Aufgabe 1: Mechanismus zuordnen

**Anforderung A: Gesamtstunden am Projekt**
Mechanismus: Rollup-Spalte

Begruendung: Die Information kommt aus verknuepften Datensaetzen (Aufgaben) und soll als Summe aggregiert werden. Ein Delay von einigen Stunden ist laut Anforderung akzeptabel. Das ist der klassische Anwendungsfall eines Rollup-Feldes.

**Anforderung B: Abweichung in Stunden pro Aufgabe**
Mechanismus: Formelspalte

Begruendung: Die Berechnung bezieht sich auf Felder desselben Datensatzes. Der Wert soll immer aktuell sein. Power Fx: `cr_StundenTatsaechlich - cr_StundenGeplant`

**Anforderung C: Projektstatus Ampel**
Mechanismus: Formelspalte

Begruendung: Die Ampelfarbe haengt vom Enddatum (Vergleich mit Today()) und dem Status-Feld ab. Beides liegt auf demselben Datensatz. Power Fx ermoeglicht die if/else-Logik:
```
If(
  And(cr_Enddatum < Today(), cr_Status <> 'Status (Projekte)'.Abgeschlossen),
  "Rot",
  If(
    And(DateDiff(Today(), cr_Enddatum, Days) < 14, cr_Status = 'Status (Projekte)'.'In Bearbeitung'),
    "Gelb",
    "Gruen"
  )
)
```

**Anforderung D: Benachrichtigung bei Budget-Ueberschreitung**
Mechanismus: Gespeichertes Feld mit Power Automate Flow

Begruendung: Eine Benachrichtigung muss ausgeloest werden. Weder Rollup noch Formelspalten koennen Trigger fuer Flows sein. Der korrekte Ansatz: Ein echtes Number-Feld "Gesamtstunden tatsaechlich" wird von einem Flow nach jeder Aufgaben-Speicherung neu summiert. Wenn sich dieser Wert aendert und die Budget-Grenze ueberschreitet, loest derselbe oder ein weiterer Flow die Benachrichtigung aus.

---

## Aufgabe 2: Fallstricke erkennen

Eine Formelspalte kann nur auf Felder desselben Datensatzes und auf Felder direkt verknuepfter Datensaetze (per Lookup, eine Ebene) zugreifen. Sie kann NICHT ueber eine 1:N-Beziehung aggregieren. Es gibt keine SUM-Funktion in Power Fx Formelspalten, die ueber eine Kindtabelle laeuft.

Der Kollege sollte stattdessen eine Rollup-Spalte erstellen, die SUM der Stunden-Felder aus der Unteraufgaben-Tabelle berechnet. Falls Echtzeit wichtig ist, alternativ ein Plugin oder Flow.

---

## Aufgabe 3: Deprecated vs. aktuell

**Vorteile der bestehenden Calculated Columns:**
- Funktionieren (heute noch)
- Kein sofortiger Handlungsbedarf

**Nachteile:**
- Microsoft empfiehlt Migration zu Formelspalten
- Neue Funktionen werden nicht mehr fuer Calculated Columns entwickelt
- Langfristig Abschaltung moeglich

**Fuer Migration spricht:**
- Formelspalten nutzen Power Fx (lesbarer, wartbarer)
- Bessere IDE-Unterstuetzung
- Zukunftssicherheit

**Bei Migration beachten:**
- Formelspalten haben einen anderen Feldnamen (neue Spalte muss angelegt werden, alte kann nicht umgewandelt werden)
- Abhängigkeiten pruefen (Views, Flows, Plugins die das Feld referenzieren)
- Testen ob Power Fx-Aequivalent existiert (nicht alle Calculated-Column-Funktionen haben direkte Power Fx Entsprechungen)

---

## Aufgabe 4: Timing ist entscheidend

**Problem mit dem Rollup-Ansatz:**
Ein Rollup-Feld "Anzahl offener Aufgaben" wird vom System mit bis zu 12 Stunden Verzoegerung aktualisiert. Wenn ein Mitarbeiter die letzte Aufgabe auf "Erledigt" setzt und der Flow sofort prueft, ob alle Aufgaben erledigt sind, liest der Flow noch den alten Rollup-Wert. Das Rollup-Feld zeigt moeglicherweise noch 1 offene Aufgabe, obwohl keine mehr offen ist.

**Korrekte Loesung:**
Der Flow wird ausgeloest, wenn eine Aufgabe auf "Erledigt" gesetzt wird. Im Flow wird dann direkt per Dataverse-Listenaktion abgefragt: "Alle Aufgaben dieses Projekts wo Status nicht gleich Erledigt und Anzahl groesser als 0?" Wenn das Ergebnis leer ist, wurden alle Aufgaben erledigt und das Projekt kann auf "Abgeschlossen" gesetzt werden. Diese Echtzeitabfrage umgeht das Timing-Problem des Rollup-Feldes vollstaendig.
