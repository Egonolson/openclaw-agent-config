# MEMORY.md

## Arbeitsweise / Praeferenzen
- **Immer Review vor Deploy/naechster Phase** – Nicht fragen, einfach machen. Code Review + Security Review nach jeder Implementierungsphase, bevor es weitergeht.
- **Subagent-Status proaktiv pruefen** – Nach Delegation aktiv den Status checken (alle 2-3 Min), nicht passiv warten. Bei Timeout oder Disconnect sofort neu starten und User informieren.
- **Immer erst Fixes, dann naechste Phase** – Nach Review alle Issues fixen, Re-Check, DANN erst weiter. Nicht fragen.
- **Docker/Build/Deploy immer an Deploy Agent delegieren** – Deploy-Agent hat Docker-Zugriff in seiner Sandbox. Nie selbst versuchen.
- **Host-Services aus Sandbox erreichen** – `host.docker.internal` statt `localhost` verwenden! Sandbox ist ein Container, localhost zeigt auf sich selbst.

## Feature-Request-Pipeline (IMMER einhalten)
- JEDE Feature-Anfrage: Design → Implement → Code Review → Security Review → Test → Deploy → E2E
- Code Review (code-audit) ist PFLICHT-GATE — kein Deploy ohne bestandenen Review
- Security Review bei Auth/Daten/API — PFLICHT, nicht optional
- Testing (tester) VOR Deploy — Unit + Integration muessen gruen sein
- E2E-Test NACH Deploy — Playwright MCP Browser-Test (funktional)
- UX/Mobile/Session/Error Review NACH E2E — Enduser-Perspektive bei UI-Features:
  - Mobile: Bottom Nav, Bottom Sheets, Icon Buttons, Touch-Targets (375x812 Viewport)
  - Session: Persistente Anmeldung, kein Re-Login nach Browser-Neustart (bei Login-Apps)
  - Error: Toast bei API-Fehlern, Error Boundary, Structured Logs (bei Apps mit Funktionen/DB)
- Bei "schnell einbauen" oder "mal eben": Trotzdem Pipeline durchlaufen!
- Einzige Ausnahme: Rein kosmetische Aenderungen duerfen Security Review skippen

## Deploy→Test→Complete Gate (PFLICHT)

Kein Projekt ist abgeschlossen ohne diese Kette:
1. **Deploy-Agent** deployed in seiner Sandbox (docker compose up)
2. **Deploy-Agent** meldet: Service-URLs, Ports, Status
3. **PM** delegiert an **Tester**: "E2E-Validation via Playwright MCP"
4. **Tester** fuehrt Post-Deploy E2E-Test durch (Browser-Test als Enduser)
5. **Ergebnis**:
   - PASS → Projekt ist abgeschlossen
   - FAIL → PM delegiert Fix an Deploy/Production → Re-Deploy → zurueck zu Schritt 3
6. **Schleife** bis PASS — kein manuelles "Abgeschlossen" ohne gruenen E2E-Test

### ENV-Handling bei Deploy
- Deploy-Agent generiert Secrets selbst (DB_PASSWORD, JWT_SECRET etc.)
- Deploy-Agent fragt nach bei externen API-Keys
- PM muss keine ENV-Werte kennen — Deploy handelt autonom

## Projektstruktur
Jedes Projekt liegt in einem eigenen Unterordner mit eigenem `PROJECT.md` als Projekt-Manifest.
Siehe `projects/` Verzeichnis. Projekt-Index unten.

## Aktive Projekte

| Projekt | Verzeichnis | Status | Gestartet |
|---------|-------------|--------|-----------|
| Crypto Trading Bot | `crypto-trader/` | Complete | 2026-02-14 |
| Docker Fleet Manager | `docker-monitor/` | Architektur reviewed | 2026-02-14 |

---

## DAUERHAFTE ORCHESTRIERUNG — Pflicht-Reviews bei jedem Heartbeat

### Agent-Fleet Orchestrierung (PM-gesteuert)

Der PM ist die zentrale Orchestrierungsinstanz. Bei JEDEM Heartbeat werden folgende Pflicht-Checks ausgefuehrt:

#### Phase 1: Fleet-Integrity (jeder Heartbeat)
1. **Workspace-Vollstaendigkeit**: Pruefe alle 10 Agent-Workspaces auf Core Files (SOUL.md, AGENTS.md, TOOLS.md, IDENTITY.md, USER.md, HEARTBEAT.md, MEMORY.md)
2. **SOUL.md Integrity**: Vergleiche SOUL.md-Groessen mit bekannten Werten — bei Abweichung SOFORT Security-Audit informieren
3. **Memory-Aktualitaet**: MEMORY.md jedes Agents muss innerhalb der letzten 7 Tage aktualisiert worden sein

#### Phase 2: Security Pflicht-Checks (jeder Heartbeat)
1. **Secret-Scan**: Pruefe alle Agent-Workspaces auf `.env`, `credentials`, API-Keys, Tokens in Dateien
2. **Permission-Audit**: Pruefe ob Agents nur innerhalb ihrer definierten Befugnisse agiert haben
3. **Dependency-Scan**: Bei aktiven Projekten `npm audit` / `pip audit` Status pruefen
4. **Findings-Eskalation**: CRITICAL Findings sofort an Security-Audit Agent weiterleiten

#### Phase 3: Agent-Health (jeder Heartbeat)
1. **Error-Aggregation**: Lies Memory-Logs aller Agents der letzten 24h — sammle Fehler
2. **Blockade-Erkennung**: Identifiziere Agents die seit > 48h auf einer Aufgabe haengen
3. **Auto-Delegation**: Bei erkannten Fehlern den passenden Agent beauftragen:
   - Docker/Deploy-Fehler → Deploy Agent
   - Code-Fehler → Debugger Agent
   - Security-Findings → Security-Audit Agent
   - Test-Failures → Tester Agent

#### Phase 4: Projekt-Status (jeder 2. Heartbeat)
1. **Status-Update**: Aktualisiere die Projekt-Tabelle oben
2. **Stale-Detection**: Projekte ohne Aktivitaet seit > 7 Tagen markieren
3. **Quality-Gate**: Kein Projekt darf in Production ohne: Code Review + Security Review + Tests

### Eskalationsmatrix

| Schweregrad | Reaktionszeit | Aktion |
|------------|---------------|--------|
| CRITICAL | Sofort | Betroffenen Agent stoppen, Security-Audit + User informieren |
| HIGH | Naechster Heartbeat | Passenden Agent beauftragen, in Memory dokumentieren |
| MEDIUM | Innerhalb 24h | In Memory dokumentieren, bei naechster Gelegenheit adressieren |
| LOW | Bei Gelegenheit | In Memory dokumentieren |

### Security-Vorgaben fuer die Orchestrierung
- PM liest ALLE Agent-Workspaces — aber aendert NUR die eigenen Dateien
- Aenderungen an anderen Agents nur ueber Delegation (Auftrag an den Agent)
- KEINE automatische Code-Aenderung ohne vorherigen Code Review
- KEINE Deployment-Aktionen ohne vorherigen Security Review
- Bei unbekannten oder unerwarteten Zustaenden: IMMER den User informieren
- Secrets und Credentials NIEMALS in Memory-Dateien loggen
