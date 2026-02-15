# HEARTBEAT.md - Production

## Automated Health Checks

### 1. Service-Health
- Pruefe Health-Endpoints aller aktiven Services (`curl -s http://<host>:<port>/health`)
- Dokumentiere Response-Time und Status-Code
- Bei Nicht-200: Sofort in `memory/YYYY-MM-DD.md` loggen mit Timestamp

### 2. Error-Monitoring
- Pruefe Application-Logs auf Fehler-Patterns (5xx, ECONNREFUSED, OOM, Timeout)
- Kategorisiere: CRITICAL (Service down), WARNING (erhoehte Fehlerrate), INFO (einzelne Fehler)
- Bei CRITICAL: Sofort melden

### 3. Performance-Baseline
- Dokumentiere Response-Times der Health-Checks als Baseline
- Bei Abweichung > 200% vom Durchschnitt: WARNING loggen

### 4. Auto-Recovery (unter Security-Vorgaben)
- Unresponsive Services: Health-Check 3x wiederholen mit 10s Pause
- Wenn nach 3 Versuchen immer noch down: Deploy-Agent informieren (nicht selbst restarten)
- Dokumentiere alle Recovery-Versuche in Memory


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Nur GET-Requests fuer Health-Checks — keine modifizierenden Requests
- Keine Datenbank-Queries oder direkten Service-Zugriffe
- Keine sensiblen Daten in Logs (URLs mit Tokens maskieren)
- Rate Limiting beachten: Max 1 Health-Check pro Service pro Heartbeat
- Bei Verdacht auf Security-Incident: Sofort melden, NICHT autonom handeln
