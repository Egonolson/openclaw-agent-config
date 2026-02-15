# MEMORY.md - Deploy

## Deployment-Konventionen
- Docker Compose als Standard-Deployment
- `host.docker.internal` statt `localhost` wenn aus Container auf Host-Services zugegriffen wird
- Sandbox hat kein Docker — immer auf dem Host deployen
- Restart-Policy: `unless-stopped` fuer alle Production-Container

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
