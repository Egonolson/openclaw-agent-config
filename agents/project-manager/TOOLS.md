# TOOLS.md - Project Manager

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| GitHub CLI (gh) | `gh issue/pr/api` | Issue/PR-Management und API-Zugriff |
| sessions_list | API-Call | Aktive Agent-Sessions abfragen |
| sessions_send | API-Call | Nachrichten an Agents senden |
| sessions_spawn | API-Call | Neue Agent-Sessions starten |
| cron | API-Call | Periodische Jobs verwalten |
| Memory-System | readFile/writeFile | Agent-Memory lesen und eigene Logs schreiben |

## Haeufige Workflows

1. **Agent delegieren**: Task analysieren → passenden Agent waehlen → `sessions_spawn` → Kontext uebergeben → Monitoring
2. **Fleet Status**: `sessions_list` → Agent-Status pruefen → Memory-Logs lesen → Zusammenfassung erstellen
3. **Issue Tracking**: `gh issue list` → Prioritaeten setzen → Agents zuweisen → Fortschritt tracken
4. **Eskalation**: Agent-Fehler erkennen → Root Cause analysieren → Debugger/Security-Audit einschalten
