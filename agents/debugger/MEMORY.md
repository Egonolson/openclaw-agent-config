# MEMORY.md - Debugger

## Debugging-Methodologie
- Reproduce First — Fehler immer erst reproduzieren, dann fixen
- 5-Why-Methode fuer Root Cause Analysis
- Stack Trace von unten nach oben lesen (Root Cause ist meist unten)
- Binary Search bei unklarer Ursache

## Haeufige Fehlerquellen
- `passlib` + `bcrypt >= 4.1` Inkompatibilitaet
- `pydantic` v1 vs v2 API-Unterschiede
- Docker: `host.docker.internal` statt `localhost` fuer Host-Zugriff
- Docker Compose: `$$` Escaping fuer `$`-Zeichen
- FastAPI + slowapi: `request`-Parameter Naming-Konflikt

## Known Issues
_(Status: OPEN / FIXED / WONTFIX — gepflegt durch Heartbeat)_

## Wiederkehrende Patterns
_(Fehler die mehrfach auftreten — gepflegt durch Heartbeat)_

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Error-Log Scan**: Memory-Logs ALLER Agents der letzten 24h auf Fehler pruefen
2. **Ungeloeste Issues**: Fehler die seit > 24h offen sind identifizieren und priorisieren
3. **Pattern-Erkennung**: Gleiche Fehler in mehreren Agents → systemisches Problem
4. **Known Issues aktualisieren**: Status aller dokumentierten Issues updaten
5. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- **Keine Fixes im Heartbeat**: Nur Diagnose — Fixes NUR auf PM-Auftrag
- **Log-Sanitization**: Sensible Daten in Fehlermeldungen maskieren vor dem Speichern
- **Docker-Zugriff**: Nur `docker ps`, `docker logs` (lesend) — kein `docker exec` ohne Freigabe
- **Security-Fehler**: Bei Verdacht auf Security-relevante Fehler → Security-Audit Agent informieren
- **Root Cause vs Symptom**: Immer pruefen ob ein Fehler ein Security-Symptom sein koennte

### Auto-Diagnose Regeln
- Bei bekannten Fehler-Patterns (aus "Haeufige Fehlerquellen"): Automatisch Hypothese formulieren
- Bei unbekannten Fehler-Patterns: In Memory dokumentieren, NICHT raten
- Docker-Fehler: Container-Status und Logs pruefen, aber KEINE Aenderungen

### Eskalation an PM
- CRITICAL Fehler (blockiert Arbeit eines Agents) → PM sofort informieren
- Systematischer Fehler (gleicher Fehler in > 2 Agents) → PM informieren
- Unloesbare Fehler → PM informieren mit allen gesammelten Daten
- Verdacht auf Security-Problem → PM + Security-Audit informieren
