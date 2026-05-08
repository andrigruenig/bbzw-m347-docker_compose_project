# Fragen – Konfiguration und Umgebungsvariablen (DL11)

Name: <Nachname> <Vorname>
Klasse: <Klasse>

---

## 1. Konfiguration

Welche Werte waren ursprünglich hardcoded in `compose.yml` und `app/main.py`?

Antwort:

In `compose.yml` waren `POSTGRES_DB=ticketdb`, `POSTGRES_USER=ticketuser`, `POSTGRES_PASSWORD=secret` sowie die komplette `DATABASE_URL` mit Host `db` und Port `5432` fest im YAML eingetragen. In `app/main.py` war dieselbe `DATABASE_URL` noch einmal direkt als String hardcoded.

---

Warum ist es ein Problem, Passwörter direkt in `compose.yml` einzutragen?

Antwort:

Weil das Passwort damit im Code und oft auch im Git-Verlauf landet. Es ist leicht versehentlich weiterzugeben, schwer sauber zu ändern und für andere sofort sichtbar.

---

Was ist der Unterschied zwischen `.env` und `.env.example`?

Antwort:

`.env` enthält die echten lokalen Werte für das laufende Projekt und wird von Docker Compose automatisch verwendet. `.env.example` ist nur eine Vorlage mit den benötigten Schlüsseln, aber ohne echte Geheimnisse.

---

Warum muss `.env` in `.gitignore` eingetragen sein?

Antwort:

Damit die echten lokalen Zugangsdaten nicht versehentlich ins Repository committet werden. So bleiben Geheimnisse lokal und werden nicht dauerhaft im Git-Verlauf gespeichert.

---

## 2. Variablen in Compose

Wie referenziert man eine Variable aus `.env` in `compose.yml`?

Antwort:

Mit `${VARIABLE_NAME}`. Beispiel: `${POSTGRES_PASSWORD}`.

---

Was passiert, wenn eine Variable in `.env` fehlt, aber in `compose.yml` verwendet wird?

Antwort:

Docker Compose ersetzt sie dann durch einen leeren Wert oder meldet eine Warnung, je nach Situation. Dadurch kann die Konfiguration unvollständig oder ungültig werden und der Start später fehlschlagen.

---

Was zeigt der Befehl `docker compose config`? Wann ist er nützlich?

Antwort:

Er zeigt die vollständig aufgelöste Compose-Konfiguration nach der Variablenersetzung. Das ist nützlich, um zu prüfen, ob alle Werte korrekt aus `.env` übernommen werden und ob die finale Konfiguration gültig ist.

---

## 3. Dockerfile und Build

Warum wird `requirements.txt` in einem eigenen `COPY`-Schritt vor dem App-Code kopiert?

Antwort:

Damit Docker den Layer mit den installierten Abhängigkeiten wiederverwenden kann, solange sich nur der Anwendungscode ändert. Das spart Build-Zeit.

---

Was bewirkt `.dockerignore`? Welche Dateien sollten darin stehen?

Antwort:

`.dockerignore` schließt Dateien aus dem Build-Kontext aus. Darin sollten zum Beispiel `.env`, `.venv/`, `__pycache__/`, `*.pyc` und weitere unnötige oder sensible Dateien stehen, damit der Build kleiner und sicherer wird.

---

## 4. Systemtest

Funktioniert `/db-check` nach Ihrer Konfigurationsanpassung?

Antwort:

Ja, nach der Umstellung auf `.env` und die Compose-Variablen sollte `/db-check` die Datenbankverbindung erfolgreich prüfen.

---

Was zeigt der Endpunkt `/db-check` an, wenn die Verbindung funktioniert?

Antwort:

Er gibt `{"db": "connected"}` zurück.

---

## 5. Reflexion

Was war der wichtigste Schritt in dieser Woche?

Antwort:

Die harte Konfiguration aus Code und Compose herauszulösen und sauber über `.env` zu verwalten.

---

Was ist noch unklar oder möchten Sie besser verstehen?

Antwort:

Ich möchte noch besser verstehen, wie man Umgebungsvariablen in größeren Projekten sauber pro Umgebung trennt, zum Beispiel für Entwicklung, Test und Produktion.
