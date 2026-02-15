# MEMORY.md - Deploy

## Deployment-Konventionen
- Docker Compose als Standard-Deployment
- `host.docker.internal` statt `localhost` wenn aus Container auf Host-Services zugegriffen wird
- Sandbox HAT Docker-Zugriff (Socket gemountet) — IMMER in der Sandbox deployen
- Projekte laufen als Container neben der Sandbox im selben Docker-Netzwerk
- Erreichbarkeit via host.docker.internal:<port> fuer den Tester
- Restart-Policy: `unless-stopped` fuer alle Production-Container

## ENV-Auto-Generation Regeln
- Passwoerter/Secrets: `openssl rand -hex 32` (ohne Rueckfrage)
- JWT RSA Keys: `openssl genrsa` + `openssl rsa -pubout` (ohne Rueckfrage)
- Externe API-Keys: IMMER nachfragen
- Ports/Hosts: Defaults aus .env.example uebernehmen
- $-Zeichen in generierten Werten: Als $$ escapen in docker-compose.yml

## Deploy→Test→Complete Gate
- Nach jedem Deploy: PM informieren mit Service-URLs + Ports
- Projekt ist NICHT abgeschlossen bis Tester E2E-Tests bestaetigt
- Bei Test-Failures: Logs analysieren, Fix deployen, erneut testen
- Schleife: Deploy → Test → Fix → Re-Deploy → Re-Test → bis gruen

## Bekannte Docker-Issues
- `$`-Zeichen in .env-Werten: Als `$$` escapen in docker-compose.yml
- Layer-Cache kann veraltete Images verursachen: `--no-cache` bei verdaechtigen Build-Fehlern
- Volume-Permissions: Container-User muss auf gemountete Volumes zugreifen koennen

## Aktive Deployments
_(Wird durch Heartbeat gepflegt — Container-Status, Ports, Health)_

## Recovery-Log
_(Automatische Restarts und deren Ergebnis — gepflegt durch Heartbeat)_

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Container-Fleet Status**: `docker ps` — alle Container pruefen (Running, Healthy, Ports)
2. **Disk-Usage**: `df -h` — Warnung bei > 85%, `docker system df` bei > 10GB unused
3. **Restart-Zaehler**: Container mit erhoehtem Restart-Count identifizieren
4. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- **Keine unbekannten Container**: Nur dokumentierte Container duerfen laufen — Fremde melden
- **Port-Exposure**: Nur explizit freigegebene Ports duerfen nach aussen offen sein
- **Image-Herkunft**: Nur Images aus vertrauenswuerdigen Registries (Docker Hub official, eigene)
- **Secrets in Logs**: Bei `docker logs` Output auf Passwoerter/Tokens pruefen und maskieren
- **Privileged Mode**: Kein Container darf mit `--privileged` laufen ohne explizite Freigabe
- Bei JEDEM Security-Fund: Sofort in Memory loggen UND PM + Security-Audit informieren

### Auto-Recovery Regeln
- Exited Container mit `unless-stopped`: Einmal `docker restart` — maximal 1x pro Heartbeat
- Nach 3 fehlgeschlagenen Restarts (aus Recovery-Log): NICHT weiter versuchen → PM melden
- KEINE Container loeschen, stoppen oder Netzwerk aendern ohne PM-Auftrag

### Eskalation an PM
- Container down seit > 1h → PM sofort informieren
- Disk > 90% → PM sofort informieren
- Unbekannter Container erkannt → PM + Security-Audit informieren
- 3x fehlgeschlagener Restart → PM informieren mit Logs
