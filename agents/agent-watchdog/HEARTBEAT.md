# HEARTBEAT.md - Agent Watchdog

## Cron-basierter Watchdog-Check

Der Watchdog hat keinen eigenen Heartbeat-Zyklus. Er wird ausschliesslich per Cron-Job gestartet wenn der PM ihn aktiviert hat.

### Bei jedem Cron-Run

1. **Projekt-Status pruefen** via PM-Abfrage
2. **Wenn kein Projekt aktiv:** Deaktiviere dich selbst: `cron({ action: "update", jobId: "agent-watchdog", patch: { enabled: false } })`. Dann `HEARTBEAT_OK` — beende sofort.
3. **Wenn Agenten aktiv:** Session-Timeouts pruefen, Delegations-Check durchfuehren
4. **Ergebnisse loggen** in `memory/YYYY-MM-DD.md`

### Timeout-Schwellwerte
- **Session-Timeout**: > 5 Min keine Aktivitaet
- **Antwort-Timeout**: 60 Sekunden nach TIMEOUT-CHECK Nachricht
- **Cron-Intervall**: Alle 5 Minuten (300.000 ms)
- **Max Laufzeit**: 180 Sekunden pro Run

## Fehlerbehandlung

- Bei Fehlern: Dokumentieren und `HEARTBEAT_OK` — NICHT autonom fixen
- Kein Retry bei fehlgeschlagenen Session-Abfragen
- Bei unerreichbarem PM: User informieren und beenden

### Security-Vorgaben
- Nur lesender Zugriff auf andere Agent-Workspaces
- Keine Code-Aenderungen
- Keine externen API-Calls
- Einzige Schreib-Aktion: eigene Memory-Logs und eigenen Cron-Job deaktivieren
