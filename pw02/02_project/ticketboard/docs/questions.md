# Fragen – Integration der Services (DL10)

Name: 
Klasse:

---

## 1. Services verstehen

Welche Services haben Sie in Ihrer `compose.yaml` definiert?

Antwort:
- **api**: FastAPI-Anwendung (REST-API)
- **db**: PostgreSQL Datenbank
- **adminer**: Web-Interface für Datenbankenverwaltung
- **frontend**: nginx Webserver für die Benutzeroberfläche

---

Welche Aufgabe hat jeder Service in Ihrem System?

Antwort:
- **api**: Stellt REST-Endpoints bereit, verwaltet Business-Logik, verbindet sich mit der Datenbank (Port 8000)
- **db**: Speichert alle Anwendungsdaten persistent in PostgreSQL (Port 5432)
- **adminer**: Ermöglicht grafische Verwaltung der Datenbank über Web-Interface (Port 8080)
- **frontend**: Stellt die Web-Benutzeroberfläche bereit und kommuniziert mit der API (Port 3000)

---

## 2. Service-Kommunikation

Welchen Servicenamen verwendet die API, um die Datenbank zu erreichen?

Antwort:
Der Servicename ist **`db`**. Dies wird in der `.env`-Datei unter `POSTGRES_HOST=db` konfiguriert. Die API verbindet sich mit `db` statt mit `localhost` oder einer IP-Adresse.

---

Warum funktioniert `localhost` innerhalb eines Containers nicht für die Kommunikation mit anderen Services?

Antwort:
`localhost` (127.0.0.1) bezieht sich auf den **lokalen Container selbst**. Wenn die API `localhost` verwendet, versucht sie, sich selbst zu erreichen, nicht die Datenbank. Jeder Container hat sein eigenes isoliertes Netzwerk-Interface. Um andere Container zu erreichen, müssen deren Namen (oder IP-Adressen) verwendet werden.

---

Wie stellt Docker Compose sicher, dass sich Services gegenseitig finden können?

Antwort:
Docker Compose erstellt ein **internes Docker-Netzwerk** (in diesem Fall `ticketnet`). Alle Services werden in dieses Netzwerk verbunden. Docker Compose registriert den Namen jedes Services als DNS-Eintrag im Netzwerk. Wenn ein Container einen anderen Service-Namen auflöst, leitet Docker es an die richtige Container-IP-Adresse weiter. Dies funktioniert ähnlich wie DNS, aber nur innerhalb des Netzwerks.

---

## 3. Ports und Zugriff

Über welche Ports sind folgende Services erreichbar?

* API
* Adminer
* Frontend

Antwort:
- **API**: Port **8000** (http://localhost:8000)
- **Adminer**: Port **8080** (http://localhost:8080)
- **Frontend**: Port **3000** (http://localhost:3000)

Diese sind die Host-Ports, über die Services von außerhalb des Docker-Systems erreichbar sind.

---

Welcher Unterschied besteht zwischen:

* Container-Port
* Host-Port

Antwort:
- **Container-Port**: Der Port, auf dem die Anwendung **innerhalb des Containers** lauscht. Z.B. FastAPI läuft intern auf Port 8000, nginx auf Port 80.
- **Host-Port**: Der Port auf dem **Host-System** (dem Computer, auf dem Docker läuft), der dem Container-Port zugeordnet ist. Z.B. Host-Port 8000 → Container-Port 8000 (api), Host-Port 3000 → Container-Port 80 (frontend).

Das Port-Mapping wird mit `ports: ["3000:80"]` definiert (Host:Container).

---

## 4. Persistenz

Was passiert mit den Daten, wenn ein Container ohne Volume gelöscht wird?

Antwort:
Alle Daten gehen **verloren**. Der Container-Speicher ist temporär und wird gelöscht, wenn der Container entfernt wird. Ohne Volumes werden die Daten nicht persistent gespeichert. Das ist ein großes Problem für Datenbanken, wo Daten dauerhaft erhalten bleiben müssen.

---

Wie haben Sie die Persistenz für die Datenbank umgesetzt?

Antwort:
Ich habe ein **Named Volume** namens `postgres_data` definiert:
```yaml
volumes:
  postgres_data:
```

Und es im `db`-Service eingebunden:
```yaml
volumes:
  - postgres_data:/var/lib/postgresql/data
```

PostgreSQL speichert seine Daten im Container im Verzeichnis `/var/lib/postgresql/data`. Das Volume sorgt dafür, dass diese Daten persistent auf dem Host gespeichert werden.

---

Warum ist ein Volume für die Datenbank notwendig?

Antwort:
Ein Volume ist notwendig, weil:
1. **Container sind ephemär**: Wenn der Container gelöscht wird, sind die Daten weg
2. **Datenbanken müssen persistent sein**: Anwendungsdaten müssen über Container-Neustart erhalten bleiben
3. **Geschäftskritische Daten**: Der Verlust von Ticketdaten wäre ein kritischer Fehler
4. **Produktion erfordert es**: Kein Produktionssystem funktioniert ohne persistente Datenspeicherung
5. **Backup und Recovery**: Volumes ermöglichen leichtere Backups und Wiederherstellung

---

## 5. Compose-Konfiguration

Welche Elemente haben Sie in Ihrer `compose.yaml` definiert?

Antwort:
- **services**: 4 Services (api, db, adminer, frontend)
- **ports**: Port-Mappings für jeden Service (Host-Port:Container-Port)
- **environment**: Umgebungsvariablen aus `.env` für API und Datenbank
- **volumes**: Named Volume `postgres_data` für Datenbank-Persistierung
- **networks**: Custom-Netzwerk `ticketnet` für Service-Kommunikation
- **depends_on**: Abhängigkeiten zwischen Services (Start-Reihenfolge)
- **build**: Build-Kontext und Dockerfile für api und frontend
- **image**: Öffentliche Images für db und adminer

---

Welche Umgebungsvariablen sind für die Datenbank-Verbindung notwendig?

Antwort:
- **POSTGRES_DB**: Name der Datenbank (`ticketdb`)
- **POSTGRES_USER**: Benutzername für PostgreSQL (`ticketuser`)
- **POSTGRES_PASSWORD**: Passwort für den Benutzer (`secret`)
- **POSTGRES_HOST**: Hostname/Service-Name der Datenbank (`db`)
- **POSTGRES_PORT**: Port der Datenbank (`5432`)

Diese sind in der `.env`-Datei definiert und werden in der `compose.yaml` mit `${VAR_NAME}` referenziert.

---

Wofür wird `depends_on` verwendet?

Antwort:
`depends_on` definiert die **Start-Reihenfolge und Abhängigkeiten** zwischen Services:
- Die Datenbank wird gestartet, bevor die API und der Adminer starten
- Die API wird gestartet, bevor das Frontend startet
- Beispiel: `api` hat `depends_on: [db]`, was bedeutet: "Starte mich nur, nachdem `db` läuft"

**Wichtig**: `depends_on` garantiert nur, dass der Container läuft, nicht dass die Anwendung bereit ist (z.B. dass die Datenbank Verbindungen annimmt). Für echte Readiness-Checks können `healthchecks` verwendet werden.

---

## 6. Systemtest

Hat das System beim ersten Start vollständig funktioniert?

Antwort:

---

Welche Probleme sind aufgetreten?

Antwort:

---

Wie haben Sie diese Probleme gelöst?

Antwort:

---

## 7. Verständnis

Beschreiben Sie kurz den Datenfluss in Ihrem System.

(Beispiel: Frontend → API → Datenbank)

Antwort:

---

Was passiert beim Befehl:

```bash
docker compose down
```

Antwort:

---

## 8. Reflexion

Was war für Sie heute die wichtigste Erkenntnis?

Antwort:

---

Was war schwierig oder noch unklar?

Antwort:
