# HEARTBEAT.md - Security Audit

## Automated Health Checks

### 1. Security-Scan
- Pruefe bekannte Vulnerability-Patterns in letzten Code-Aenderungen
- Checke ob `.env`-Dateien, Credentials oder API-Keys in Git-History aufgetaucht sind
- Pruefe Dependency-Advisories: `npm audit --json` / `pip audit` fuer aktive Projekte

### 2. Agent-Security
- Pruefe ob Agent-Workspaces unerwartete Dateien enthalten (z.B. `.env`, Credentials)
- Pruefe ob SOUL.md-Dateien unveraendert sind (Hash-Vergleich mit letztem bekannten Stand)
- Dokumentiere Auffaelligkeiten in `memory/YYYY-MM-DD.md`

### 3. Access-Patterns
- Pruefe ob Agents ausserhalb ihrer Befugnisse agiert haben (Log-Analyse)
- Identifiziere ungewoehnliche Datei-Zugriffe oder Netzwerk-Aktivitaeten

### 4. Compliance
- Pruefe ob alle Agents ihre Security-Vorgaben in HEARTBEAT.md haben
- Pruefe ob Memory-Dateien sensible Daten enthalten (Passwoerter, Tokens, Keys)
- Bei Fund: Sofort melden und betroffene Datei markieren

### 5. Senior-Expert-Checks
- **CVE-Datenbank-Check**: Offene CVEs in Projekt-Dependencies gegen NVD/OSV.dev pruefen
- **SARIF-Report-Aggregation**: Semgrep-Reports auf neue HIGH/CRITICAL Findings scannen
- **Variant Analysis Trigger**: Bei neuem Finding → Semgrep-Pattern auf gesamte Codebase anwenden
- **Entry-Point-Inventar**: Neue API-Endpoints/Eingangspunkte seit letztem Heartbeat identifizieren


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Dieser Agent hat erweiterte Leserechte fuer Sicherheitsanalyse
- KEINE Code-Aenderungen, KEINE Datei-Loeschungen
- Findings sofort dokumentieren mit Schweregrad und Empfehlung
- Bei CRITICAL: Sofort melden, auch ausserhalb der Active Hours
- Keine automatische Behebung — nur Analyse und Empfehlung