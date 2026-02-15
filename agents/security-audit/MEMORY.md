# MEMORY.md - Security Audit

## Security-Standards
- OWASP Top 10 als Mindeststandard
- Dependency-Scanning: `npm audit` / `pip audit` regelmaessig
- Keine Secrets in Git-History (auch nicht in alten Commits)
- Agent-Workspaces: Keine .env, Credentials oder API-Keys

## SOUL.md Integrity Hashes
_(SHA256-Hashes der SOUL.md-Dateien aller Agents — Referenzwerte fuer Integritaetspruefung)_

## Bekannte Risiken
_(Wird durch Heartbeat gepflegt — aktive Findings mit Status)_

## Compliance-Status
_(Status der Security-Vorgaben pro Agent — gepflegt durch Heartbeat)_

## Incident-History
_(Security-relevante Vorfaelle und deren Behebung)_

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Agent-Workspace Scan**: Alle 10 Workspaces auf `.env`, Credentials, API-Keys, Tokens durchsuchen
2. **SOUL.md Integritaet**: Dateigroesse und Hash gegen Referenzwerte pruefen
3. **Heartbeat-Compliance**: Jeder Agent muss Security-Vorgaben in seiner HEARTBEAT.md haben
4. **Memory-Scan**: Pruefe ob Memory-Dateien sensible Daten enthalten
5. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- **Secret-Detection**: Regex-Scan auf Patterns: `password=`, `api_key=`, `token=`, `secret=`, Base64-encoded Strings
- **SOUL.md Tampering**: Bei Abweichung von Referenz-Hash → CRITICAL — sofort PM + User informieren
- **Privilege Escalation**: Pruefe ob Agents Aktionen ausserhalb ihrer Befugnisse ausfuehren
- **Dependency-CVEs**: Bekannte CVEs in genutzten Packages identifizieren
- **Container-Security**: Pruefe ob Container mit `--privileged`, als root, oder mit Host-Netzwerk laufen
- **Network Exposure**: Unerwartete offene Ports oder externe Verbindungen melden

### Severity-Matrix

| Finding | Schweregrad | Beispiel |
|---------|------------|---------|
| Secret in Workspace | CRITICAL | API-Key in MEMORY.md |
| SOUL.md manipuliert | CRITICAL | Hash stimmt nicht |
| Agent ueberschreitet Befugnisse | HIGH | Deploy-Agent aendert Code |
| Dependency mit bekannter CVE | HIGH | kritischer npm advisory |
| Fehlende Security-Vorgaben | MEDIUM | HEARTBEAT.md ohne Security-Section |
| Veraltete Dependencies | LOW | Minor Version behind |

### Eskalation an PM
- CRITICAL Fund → PM + User SOFORT informieren, betroffenen Agent markieren
- HIGH Fund → PM im naechsten Heartbeat informieren
- Systematisches Muster (gleicher Fund in mehreren Agents) → PM mit Empfehlung informieren
- Compliance-Luecke → PM informieren mit konkretem Behebungsvorschlag
