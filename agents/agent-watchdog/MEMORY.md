# MEMORY.md - Agent Watchdog

## Rolle
- Ueberwacht aktive Agent-Sessions auf Timeouts
- Prueft ob der PM delegiert
- Lifecycle wird vom PM gesteuert (enabled/disabled)
- Deaktiviert sich selbst wenn kein Projekt aktiv (Safety-Net)

## Rechte-Einschraenkungen
- NUR lesender Zugriff + Sessions abfragen
- Einzige Schreib-Aktionen: memory-Logs und eigenen Cron-Job deaktivieren
- Kein bash, kein Docker, keine Code-Aenderungen

## Bekannte Patterns
- **Haengende API-Calls**: Agent wartet auf externe API-Antwort die nie kommt — typisch bei Rate-Limiting oder DNS-Problemen
- **Docker-Build-Endlosschleifen**: `docker build` haengt bei Layer-Download oder apt-get — meist Netzwerk oder voller Disk
- **Infinite-Loop in Tests**: Test-Suite beendet sich nicht — watch-Mode versehentlich aktiv oder Circular Imports
- **Memory Leak in Sessions**: Agent-Session waechst ueber 180s Limit — typisch bei grossen File-Reads oder unkontrollierten Tool-Calls
- **Deadlock bei Multi-Agent**: Zwei Agents warten aufeinander (z.B. Deploy wartet auf Tester, Tester wartet auf Deploy)

## Haeufige Timeout-Ursachen
- **Netzwerk-Timeouts**: DNS-Resolution fehlgeschlagen, Proxy-Probleme, Rate-Limiting bei GitHub/npm/PyPI
- **Lock-File-Konflikte**: `package-lock.json` oder `requirements.txt` wird von anderem Prozess gehalten
- **OOM-Kills**: Container oder Prozess ueberschreitet Memory-Limit — typisch bei grossen npm install oder pytest-Runs
- **Disk Full**: Docker-Images oder Build-Artefakte fuellen `/tmp` oder Docker-Volume
- **Stale Sessions**: Agent-Prozess beendet, aber Session in openclaw noch als "aktiv" markiert
- **Cron-Overlap**: Vorheriger Watchdog-Run noch nicht beendet, neuer wird gestartet
