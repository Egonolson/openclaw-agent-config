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
_(Wird durch Watchdog-Runs befuellt)_

## Haeufige Timeout-Ursachen
_(Wird durch Erfahrung befuellt)_
