# SPEC.md · Praxis Demir & Kollegen (K5)

Quelle: Interview mit Dr. Aylin Demir. M1.6 = Meeting 1, Antwort 6; M2.3 = Meeting 2, Antwort 3. IDs und Feldnamen sind Modellfestlegungen.

## Entitäten

| Entität | Attribute |
|---|---|
| Patient | patient_id, name, geburtsdatum, versichertennummer bzw. praxisnummer, telefon, email (nur mit Zustimmung), hausarzt → Arzt |
| Arzt | arzt_id, name |
| MFA | mfa_id, name, rolle |
| Termin | termin_id, patient → Patient, arzt → Arzt, typ → Termintyp, beginn, dauer, buchungsweg (Telefon, Walk-in, online), status (u. a. no_show, entschuldigt) |
| Termintyp | typ_id, bezeichnung, standarddauer; später: fragebogenmodus, pflichtangaben |
| Zeitfenster | fenster_id, arzt → Arzt, beginn, ende, art (regulär, akut) |
| Sperrzeit | sperrzeit_id, beginn, ende, grund, geltungsbereich (ein Arzt oder gesamte Praxis) |
| Rezeptanfrage | anfrage_id, patient → Patient, arzt → Arzt, medikament, dosierung, eingegangen_am, status (u. a. offen, freigegeben) |

Kassenpatienten werden über die Versichertennummer, Privatpatienten und Selbstzahler über die Praxisnummer identifiziert. E-Mail ist freiwillig und setzt die Zustimmung zur Kontaktaufnahme voraus. (M1.10)

## Beziehungen

- Patient 1:n Termin
- Arzt 1:n Termin
- Termintyp 1:n Termin
- Arzt n:m Termintyp (M1.6, M2.6)
- Arzt 1:n Patient über die Hausarztzuordnung; im Modell höchstens ein Hausarzt je Patient, Pflichtzuordnung offen (M2.6)
- Arzt 1:n Zeitfenster
- Arzt 1:n persönliche Sperrzeit; Praxisschließungen gelten für alle Ärzte
- Patient 1:n Rezeptanfrage
- Arzt 1:n Rezeptanfrage; zugewiesener Arzt kann bei Vertretung wechseln

## Business Rules

- **BR1 · Termintyp:** Vorsorge 20 Minuten, Beratung 15 Minuten, Impfung 10 Minuten. Vorsorge und Beratung bei allen drei Ärzten, Impfung nur bei Dr. Demir. Hausarzt bevorzugen, besonders bei Vorsorge; bei Bedarf auf geeignete Kollegen ausweichen. Rezeptabholung ohne Termin. (M1.6, M2.6)
- **BR2 · Verfügbarkeit:** Nur freie Zeitfenster buchen; Arbeitszeiten, Pausen und Hausbesuche berücksichtigen. Persönliche Abwesenheit sperrt den betroffenen Arzt, Praxisschließung alle Ärzte. MFA darf Termine verschieben. (M1.7–8, M1.11)
- **BR3 · Akutslots:** Fester Pool je Vor- und Nachmittag, nicht vorab regulär vergeben. MFA darf eigenständig buchen und dringende Grenzfälle zulassen, ohne ärztliche Freigabe. (M1.7–8)
- **BR4 · No-Show:** Nach zwei unentschuldigten Ausfällen innerhalb eines Jahres Hinweisbrief; bei drei innerhalb zwölf rollierender Monate automatische Online-Sperre. Durch MFA entschuldigte Fälle zählen nicht. Telefonische Buchung mit Sperrhinweis bleibt möglich. Nur manuelle Freischaltung durch MFA oder Dr. Demir, keine automatische Aufhebung. Keine Gebühren. (M1.9–10, M2.5)
- **BR5 · Rezepte:** Anfrage für MFA sichtbar; nur der zugewiesene Arzt darf freigeben. MFA darf nur bei markierter Abwesenheit des bisherigen Arztes umleiten. Bei Rückfragen bleibt die Anfrage offen; medizinische Ausnahmen entscheidet der Arzt. Nach Freigabe: Benachrichtigung und Abholung. Gewünschte Frist: Eingang bis 12 Uhr → Freigabe bis zum nächsten Vormittag. (M1.2–5, M1.8, M2.7)

## Widersprüche

- **W1** Prüfung „am Abend“ (M1.1) vs. „am nächsten Morgen“ (M1.2) → bisher auslastungsabhängig, künftig gewünschte Frist gemäß BR5. (M1.3)
- **W2** Fragebogen nach Buchung (M1.12) vs. keine Buchung ohne kritische Angaben (M2.2) → je Termintyp: Impfung erst nach Pflichtangaben bestätigen; Beratung sofort bestätigen, Fragebogen freiwillig danach. (M2.3)

## Prioritäten

1. Online-Terminbuchung, korrekte Arzt-/Typzuordnung, Patientenidentifikation und automatische No-Show-Sperre (v1; M1.10–11)
2. Rezeptanfrage → ärztliche Freigabe → Benachrichtigung → Abholung (v1; M2.7)
3. Termintypabhängiger Fragebogen (später; M1.12, M2.1–4)
4. Apotheken-Anbindung (später; M2.7)

## Neue Anforderung · Fragebogen

Kurzer Fragebogen je Termintyp; Antworten als Notiz beim Termin, vorab für MFA und Ärztin sichtbar. Modus und Pflichtangaben je Typ konfigurierbar. (M1.12, M2.1–3)

| Typ | Fragen | Bestätigung |
|---|---|---|
| Beratung | Hauptbeschwerde, seit wann, bekanntes oder neues Problem | Sofort; Fragebogen danach freiwillig |
| Impfung | Impfstoff, letzte Impfung, bekannte Unverträglichkeiten | Nach Absenden mit kritischen Angaben; Unverträglichkeiten ausdrücklich verpflichtend |

Impfslot vorläufig reservieren; fünf Minuten als verhandelbarer Richtwert. Bei Ablauf sofort verständlich informieren und Rückweg zur Terminauswahl anbieten, auch während der Eingabe. (M2.3–4)

## Offene Fragen

- Zählen alte No-Shows nach manueller Freischaltung für eine erneute Sperre weiter?
- Rezeptfrist: genaue Uhrzeit sowie Umgang mit Eingang nach 12 Uhr, Schließtagen und Rückfragen?
- Anzahl der Akutslots je Halbtag und genaue Freigabezeiten?
- Hausarztzuordnung immer verpflichtend?
- Weitere Pflichtfragebögen/-angaben und endgültige Reservierungsdauer?
- Soll bei Änderungen die handelnde MFA gespeichert werden?
