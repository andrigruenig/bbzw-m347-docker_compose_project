# Statusbericht – Woche 3 (DL11)

Name: <Andri> <Grünig>
Klasse: <24aL>

---

## Umgesetzte Arbeiten

- Konfiguration von API und Datenbank auf Umgebungsvariablen umgestellt.
- `.env`, `.gitignore` und `.dockerignore` ergänzt.
- `docs/questions.md` beantwortet und die Dokumentation aktualisiert.

---

## Aktueller Stand

- Das TicketBoard ist so vorbereitet, dass es mit `docker compose up --build` gestartet werden kann und die Datenbankverbindung über `DATABASE_URL` läuft.

---

## Offene Probleme / Herausforderungen

- Die lokalen Geheimnisse müssen in `.env` gepflegt werden; ohne passende Werte startet die Datenbankverbindung nicht korrekt.

---

## Nächste Schritte

- Systemstart und Endpunkte erneut testen.
- Optional Umgebungen für Entwicklung und Produktion noch klarer trennen.

---

## Selbsteinschätzung (optional)

