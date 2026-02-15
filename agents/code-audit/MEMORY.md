# MEMORY.md - Code Audit

## Audit-Standards
- OWASP Top 10 als Basis-Checkliste
- Severity-Levels: CRITICAL, HIGH, MEDIUM, LOW
- Jedes Finding braucht: Beschreibung, Schweregrad, betroffene Datei, Empfehlung

## Bekannte Patterns / Anti-Patterns
_(Wird durch Heartbeat gepflegt — hier kommen wiederkehrende Findings rein)_

## Offene Findings
_(Wird durch Heartbeat gepflegt — Status-Tracking offener Issues)_

## Lessons Learned
- Immer die gesamte Dependency-Chain pruefen, nicht nur direkte Imports
- Docker-Compose Secrets: Nie in docker-compose.yml hardcoden, immer .env nutzen

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Ungepruefte Aenderungen**: Git-Diff seit letztem Audit identifizieren
2. **Offene Findings nachverfolgen**: Fruehere CRITICAL/HIGH Findings auf Fix-Status pruefen
3. **Code-Quality Trends**: Wiederkehrende Anti-Patterns dokumentieren
4. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- **SQL Injection**: Prepared Statements / Parameterized Queries in jedem DB-Zugriff
- **XSS**: Input-Sanitization und Output-Encoding pruefen
- **Auth-Bypass**: Fehlende oder fehlerhafte Authentifizierung/Autorisierung
- **Secret Exposure**: Hardcoded Secrets, API-Keys, Passwoerter in Code
- **Dependency Vulnerabilities**: Bekannte CVEs in genutzten Packages
- Bei JEDEM Fund >= HIGH: Sofort in Memory loggen UND PM informieren

### Eskalation an PM
- Neuer CRITICAL/HIGH Fund → PM sofort informieren
- Offener CRITICAL Fund seit > 48h → PM eskalieren
- Wiederkehrendes Pattern erkannt → PM informieren mit Empfehlung fuer systemische Loesung
