# HEARTBEAT.md - Deploy

## Automated Health Checks

### 1. Container-Status
- Pruefe laufende Docker-Container: `docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"`
- Identifiziere Container mit Status "Restarting", "Exited", oder ungesundem Health-Check
- Bei Problemen: Logs der letzten 50 Zeilen pruefen (`docker logs --tail 50 <container>`)

### 2. Resource-Check
- Pruefe Disk-Usage: `df -h` — Warnung bei > 85% Auslastung
- Pruefe Docker-Disk: `docker system df` — Warnung bei > 10GB unused
- Dokumentiere Ressourcen-Status in `memory/YYYY-MM-DD.md`

### 3. Deployment-Log
- Pruefe ob anstehende Deployments existieren (aus PM-Auftraegen)
- Dokumentiere letzte erfolgreiche/fehlgeschlagene Deployments

### 4. Auto-Recovery (unter Security-Vorgaben)
- Container mit Status "Exited" und Restart-Policy "unless-stopped": Einmal `docker restart` versuchen
- MAXIMAL 1 Auto-Restart pro Container pro Heartbeat-Zyklus
- Nach 3 fehlgeschlagenen Restarts: Nicht weiter versuchen, sondern melden

### 5. Senior-Expert-Checks
- **Trivy Image Scan**: `trivy image --severity HIGH,CRITICAL` auf alle aktiven Container-Images
- **SBOM Generierung**: SBOM (SPDX-JSON) bei jedem neuen Build erzeugen und versionieren
- **Base Image Provenance**: Pruefen ob Base-Images Digest-pinned sind (nicht `:latest`)
- **Runtime Security**: Read-only Filesystem und Capability Dropping fuer alle Container validieren


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Kein `docker rm` oder `docker rmi` ohne explizite Freigabe
- Keine Port-Aenderungen oder Netzwerk-Konfiguration
- Kein Zugriff auf Container-Secrets oder Env-Variablen in Logs
- `docker logs` Output: Secrets filtern (Passwoerter, API-Keys, Tokens)
- Bei unbekannten Containern: Dokumentieren, NICHT stoppen oder entfernen