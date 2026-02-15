# HEARTBEAT.md - Debugger

## Automated Health Checks

### 1. Error-Log Monitoring
- Pruefe `memory/YYYY-MM-DD.md` aller Agents auf offene Fehler-Reports
- Identifiziere Fehler die seit > 24h ungeloest sind
- Kategorisiere: CRITICAL (blockiert Arbeit), WARNING (degradiert), INFO (kosmetisch)

### 2. Bekannte Issues Tracker
- Lies MEMORY.md und pruefe ob dokumentierte Workarounds noch aktuell sind
- Identifiziere wiederkehrende Fehler-Patterns (gleicher Fehler in mehreren Logs)
- Dokumentiere neue Patterns in `memory/YYYY-MM-DD.md`

### 3. Auto-Diagnose (unter Security-Vorgaben)
- Bei bekannten Fehler-Patterns: Root Cause Hypothese dokumentieren
- Vorgeschlagene Fixes in Memory loggen — NICHT automatisch anwenden
- Bei Docker-Fehlern: Container-Status und Logs pruefen (nur lesend)

### 4. Memory-Hygiene
- Konsolidiere Debug-Erkenntnisse in MEMORY.md
- Pflege eine "Known Issues" Liste mit Status (OPEN/FIXED/WONTFIX)

### 5. Senior-Expert-Checks
- **Structured Logging Compliance**: Neue Log-Statements auf JSON-Format pruefen
- **Regression Pattern Scan**: Letzte 10 Commits auf bekannte Regressions-Muster scannen
- **Git Bisect Readiness**: Test-Suite muss fuer automatisiertes `git bisect run` geeignet sein


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Nur lesender Zugriff auf Logs und Container-Informationen
- Keine Code-Aenderungen im Heartbeat — nur Diagnose
- Keine `docker exec` Befehle ohne explizite Freigabe
- Fehler-Logs: Sensible Daten vor dem Speichern maskieren
- Bei Verdacht auf Security-relevante Fehler: Security-Audit Agent informieren