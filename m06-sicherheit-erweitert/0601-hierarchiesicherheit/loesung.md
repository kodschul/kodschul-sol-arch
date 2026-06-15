# Lab 6.1 - Loesung: Hierarchiesicherheit gezielt einsetzen

## Aufgabe 1: Eignung bewerten

**Dafuer spricht:**

- Klare Berichtslinien (kein Matrixmodell)
- Practice Lead → Senior Consultant → Consultant ist eine einfache Hierarchie
- Kein explizites Compliance-Erfordernis fuer auditierbare Zugriffsrechte erwaehnt

**Dagegen spricht:**

- Manager-Feld in AD ist nicht zuverlaessig gepflegt ("so gut es geht")
- Das ist ein KO-Kriterium: Hierarchiesicherheit ist nur so zuverlaessig wie die AD-Daten
- 3 Wochen Verzoegerung bei Aenderungen (laut Aufgabe 3) macht das System unzuverlaessig

**Voraussetzung fuer Funktionieren:**

- Manager-Feld muss fuer alle 600 Mitarbeiter korrekt gepflegt sein
- SLA mit HR: Aenderungen innerhalb von 24h im AD reflektiert
- Monitoring: Wer hat aktuell kein Manager-Feld? (= Sicherheitsluecke)

**Alternative: BU-Hierarchie**

- Practice-Ebene als BU, Senior-Consultants als Unter-BU oder auf gleicher Ebene
- Practice Lead erhaelt "Parent:Child"-Tiefe auf Projekt-Tabelle
- Unabhaengig von AD-Pflege, explizit auditierbar, stabil bei Personalwechseln
- Mehr Konfigurationsaufwand, dafuer verlasslicher

**Empfehlung:** BU-Hierarchie, solange die AD-Pflege nicht auf einen verlasslichen Standard gebracht wurde. Nach Verbesserung der AD-Daten kann Hierarchiesicherheit als einfachere Ergaenzung erwaegt werden.

---

## Aufgabe 2: Tiefenkonfiguration

Hierarchiekette:

```
Practice Lead (Ebene 0)
  └── Senior Consultant (Ebene 1 - direkter Bericht)
        └── Consultant (Ebene 2 - Bericht des Berichts)
```

Ein Practice Lead braucht **Tiefe 2**, um alle Projektdatensaetze zu sehen:

- Tiefe 1: Sieht Projekte der Senior Consultants (direkte Berichte)
- Tiefe 2: Sieht Projekte der Consultants (Berichte der Berichte)

Konfiguration: In den Hierarchiesicherheits-Einstellungen der Umgebung "Tiefe: 2" einstellen.

**Hinweis:** Die Tiefe gilt global fuer die Umgebung, nicht pro Nutzer oder Rolle. Das bedeutet, auch Senior Consultants wuerden mit Tiefe 2 die Datensaetze aller Consultants ihrer Mitarbeiter sehen - falls sie die entsprechende Sicherheitsrolle haben.

---

## Aufgabe 3: Risikoszenario

**Zugriffsproble in den drei Wochen:**

1. **Zu viel Zugriff:** Der Senior Consultant sieht noch alle Projekte seines alten Fachbereichs (da sein altes Manager-Feld noch gesetzt ist). Er sieht damit Daten, fuer die er nicht mehr zust\u00e4ndig ist.

2. **Zu wenig Zugriff:** Sein neuer Practice Lead sieht seine neuen Projekte nicht (weil der Manager-Eintrag noch auf den alten Practice Lead zeigt).

3. **Falscher Practice Lead hat Zugriff:** Der alte Practice Lead sieht weiterhin die Projekte des Senior Consultants, obwohl er nicht mehr fuer ihn zust\u00e4ndig ist.

**Empfehlung als SA:**

- Kurzfristig: Manuelles Sharing des Senior Consultants Datensaetze an den neuen Practice Lead bis AD aktualisiert ist
- Mittelfristig: SLA mit HR fuer AD-Aktualisierungen bei Personalwechseln (max. 1 Werktag)
- Langfristig: Monitoring-Flow, der taeglich prueft ob Nutzer ohne Manager-Feld existieren und eine Benachrichtigung an HR sendet
- Strategisch: Dieses Szenario ist ein Argument dafuer, BU-Hierarchie statt Hierarchiesicherheit einzusetzen, da BU-Zuweisungen explizit und unabhaengig von AD sind
