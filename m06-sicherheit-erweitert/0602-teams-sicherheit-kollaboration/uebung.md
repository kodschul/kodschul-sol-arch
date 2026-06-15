# Lab 6.2 - Uebung: Teams als Sicherheits- und Kollaborationswerkzeug bewerten

## Szenario

Ein Krankenhaus betreibt eine Dataverse-Loesung fuer Patientenakten und Behandlungsplaene. Die IT-Abteilung hat bereits Azure AD-Gruppen fuer alle Stationen gepflegt (Chirurgie, Innere Medizin, Notaufnahme, etc.).

**Anforderungen:**

1. Patientenakten gehoeren der Station, nicht dem einzelnen Arzt
2. Wenn ein Patient von einer Station in eine andere verlegt wird, soll die neue Station Zugriff erhalten - die alte aber behalten
3. Ein Konsiliararzt aus einer anderen Station soll temporaer einzelne Patientenakten lesen koennen
4. Die IT will Mitgliedschaften aus Azure AD verwalten, nicht in Dataverse

---

## Aufgabe 1: Team-Typen zuordnen (15 Minuten)

Weise den drei beschriebenen Anforderungen (1, 2, 3) je einen Team-Typ zu. Begruende jede Entscheidung.

| Anforderung                            | Empfohlener Team-Typ | Begruendung |
| -------------------------------------- | -------------------- | ----------- |
| 1. Patientenakten gehoeren der Station |                      |             |
| 2. Neue Station bei Verlegung          |                      |             |
| 3. Konsiliararzt temporaer             |                      |             |

---

## Aufgabe 2: AAD Group Teams konfigurieren (15 Minuten)

Die IT will AAD Group Teams einrichten. Beschreibe den Einrichtungsprozess und identifiziere dabei:

1. Welche BU bekommt das Team "Station Chirurgie"?
2. Welche Sicherheitsrolle erhaelt das Team?
3. Was muss IT bei der Synchronisierungsverzoegerung beachten?

---

## Aufgabe 3: Fallstrick bei Rollenvererbung (10 Minuten)

Das Team "Station Notaufnahme" erhaelt eine Sicherheitsrolle mit Read-Org auf alle Patientenakten (wegen der Dringlichkeit in der Notaufnahme).

Ein Arzt aus der Chirurgie wird temporaer als Notarzt eingeteilt und dem Notaufnahme-Team hinzugefuegt.

Was passiert mit seinen Berechtigungen? Ist das gewollt oder ein Problem?
