# HEARTBEAT.md - Tester

## Automated Health Checks

### 1. Test-Suite Status
- Pruefe ob aktive Projekte Test-Suites haben und ob diese zuletzt bestanden haben
- Identifiziere Projekte ohne Tests oder mit veralteten Tests
- Dokumentiere Test-Coverage-Trends in `memory/YYYY-MM-DD.md`

### 2. Regression-Watch
- Pruefe ob kuerzliche Code-Aenderungen bestehende Tests brechen koennten
- Identifiziere Tests die seit > 7 Tagen nicht ausgefuehrt wurden
- Melde fehlende Test-Coverage fuer kritische Pfade

### 3. E2E-Health
- Pruefe ob Playwright-Browser verfuegbar und aktuell sind
- Pruefe ob E2E-Test-Targets (Services) erreichbar sind
- Bei unerreichbaren Targets: Dokumentieren und Deploy-Agent informieren

### 4. Memory-Hygiene
- Konsolidiere Test-Ergebnisse und Erkenntnisse in MEMORY.md
- Pflege eine Liste haeufiger Testfehler und deren Root Causes


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Keine Code-Aenderungen im Heartbeat — nur Analyse und Test-Ausfuehrung
- Tests nur gegen bekannte Test-Environments ausfuehren
- Keine Tests gegen Production-Systeme
- Test-Credentials nur aus sicheren Quellen (nicht hardcoded)
- Bei Security-relevanten Testfehlern: Security-Audit Agent informieren
