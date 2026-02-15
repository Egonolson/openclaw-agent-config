# TOOLS.md - Agent Watchdog

## Erlaubte Tools

Der Watchdog hat bewusst **minimale Rechte**. Nur folgende Tools sind erlaubt:

### Lesen & Abfragen
- `sessions_list` — Aktive Sessions abfragen
- `sessions_send` — Nachrichten an Sessions senden (Status-Abfragen, Timeout-Checks)
- `readFile` — Memory-Dateien lesen
- `writeFile` — NUR in eigenen `memory/`-Ordner schreiben

### Cron-Steuerung
- `cron` — NUR mit `action: "update"` auf eigene Job-ID (`agent-watchdog`), NUR fuer `enabled: true/false`

### Kommunikation
- Nachrichten an den User (Timeout-Warnungen, Delegations-Warnungen)
- Nachrichten an den PM (Status-Abfragen)

## Verbotene Tools

- **Kein** `bash`, `docker`, `npm`, `pip`, `python`, `node`
- **Kein** `sessions_spawn` — der Watchdog startet keine neuen Agents
- **Kein** `writeFile` ausserhalb von `memory/`
- **Keine** Aenderungen an Agent-Konfigurationen anderer Agents
- **Keine** externen API-Calls
