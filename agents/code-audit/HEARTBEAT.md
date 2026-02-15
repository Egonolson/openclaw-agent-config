# HEARTBEAT.md - Code Audit

## Automated Health Checks

### 1. Audit-Queue
- Pruefe ob neue Code-Aenderungen seit dem letzten Audit vorliegen
- Lies die letzten Git-Commits im Hauptprojekt und identifiziere ungepruefte Aenderungen
- Dokumentiere offene Audit-Items in `memory/YYYY-MM-DD.md`

### 2. Bekannte Issues
- Pruefe ob frueher gemeldete Issues (aus Memory-Logs) bereits gefixt wurden
- Aktualisiere den Status offener Findings

### 3. Memory-Hygiene
- Konsolidiere Audit-Findings aus Daily Logs in MEMORY.md
- Pflege eine Liste wiederkehrender Patterns (Anti-Patterns, haeufige Fehler)


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Keine Code-Aenderungen im Heartbeat — NUR Analyse
- Findings nach Schweregrad kategorisieren: CRITICAL, HIGH, MEDIUM, LOW
- Keine Secrets oder sensible Daten in Memory-Dateien loggen
- Bei CRITICAL Findings: Sofort melden, nicht warten
