# MEMORY.md - Tester

## Test-Konventionen
- Test Pyramid: Viele Unit Tests, weniger Integration, gezielte E2E
- Vitest fuer Unit Tests, Playwright fuer E2E
- Bug-First: Bei Bugfix immer erst fehlschlagenden Test schreiben
- Characterization Tests fuer Legacy-Code ohne Tests

## Edge Cases Checkliste
- Leere Strings, null, undefined
- Leere Arrays/Objekte
- Unicode, Emojis, RTL, Zero-Width
- Zeitzonen, Schaltjahr, DST
- Concurrent Requests (Race Conditions)
- Netzwerk-Timeouts

## Playwright-MCP E2E-Konventionen
- Browser-Tests via Playwright MCP Server (`@anthropic-ai/mcp-playwright`)
- Immer `host.docker.internal:<port>` fuer deployed Services (nicht localhost)
- Screenshots bei jedem Fehler speichern
- Console-Logs erfassen fuer Debugging
- Projekt ist NICHT abgeschlossen bis E2E-Validation PASS meldet
- Bei FAIL: Fehlerliste an PM + Deploy, dann Re-Test nach Fix

## Post-Deploy Checkliste
1. Health-Endpoint erreichbar?
2. Frontend laedt? (kein White Screen, kein 500)
3. Console-Errors? (JavaScript-Fehler im Browser)
4. Navigation funktioniert? (Links, Routing)
5. Login/Auth funktioniert? (wenn vorhanden)
6. Kern-Feature nutzbar? (Hauptfunktion testen)

## Bekannte Test-Environments
_(Service-URLs und Credentials-Quellen — gepflegt durch Heartbeat)_

## Haeufige Testfehler
_(Root Causes und Loesungen — gepflegt durch Heartbeat)_

## Test-Coverage Status
_(Pro Projekt — gepflegt durch Heartbeat)_

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Test-Suite Health**: Aktive Projekte auf Test-Existenz und letzte Ausfuehrung pruefen
2. **Coverage-Tracking**: Test-Coverage fuer kritische Pfade dokumentieren
3. **Regression-Watch**: Neue Code-Aenderungen die Tests brechen koennten identifizieren
4. **E2E-Readiness**: Playwright-Browser und Test-Targets auf Verfuegbarkeit pruefen
5. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- **Keine Production-Tests**: Tests NIEMALS gegen Production-Systeme ausfuehren
- **Test-Credentials**: Nur aus sicheren Quellen (Environment-Variablen, nicht hardcoded)
- **Security-Test-Coverage**: Pruefe ob Auth-Flows, RLS-Policies und Input-Validation getestet sind
- **Test-Data Cleanup**: Nach Tests muessen Test-Daten bereinigt werden (keine Leaks)
- **Playwright Security**: Browser-Tests duerfen keine externen URLs aufrufen ohne Freigabe
- Bei Security-relevanten Test-Failures → PM + Security-Audit informieren

### Pflicht-Test-Szenarien (bei jedem neuen Feature pruefen)
- Auth-Bypass: Kann man ohne Login auf geschuetzte Routen zugreifen?
- RLS: Kann User A auf Daten von User B zugreifen?
- Input Validation: Werden XSS/SQLi-Payloads korrekt abgefangen?
- Rate Limiting: Greift die Begrenzung bei Brute-Force?

### Eskalation an PM
- Kritische Tests fehlgeschlagen → PM sofort informieren
- Test-Coverage < 50% fuer kritischen Pfad → PM informieren
- Security-Test deckt Luecke auf → PM + Security-Audit informieren
- E2E-Targets nicht erreichbar → PM + Deploy informieren
