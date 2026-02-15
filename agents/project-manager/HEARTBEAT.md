# HEARTBEAT.md - Project Manager

## Automated Health Checks

Fuehre bei jedem Heartbeat folgende Checks durch. Dokumentiere Ergebnisse in `memory/YYYY-MM-DD.md`.

### 1. Agent-Fleet Status
- Pruefe ob alle Agent-Workspaces vollstaendig sind (SOUL.md, AGENTS.md, TOOLS.md, IDENTITY.md, USER.md, HEARTBEAT.md)
- Liste fehlende Dateien auf und erstelle sie aus Templates (`docs/reference/templates/`)
- SECURITY: Nutze `writeFileIfMissing()` Logik — NIEMALS existierende Dateien ueberschreiben

### 2. Projekt-Status
- Lies `memory/YYYY-MM-DD.md` der letzten 2 Tage fuer alle Agents
- Identifiziere blockierte oder fehlerhafte Projekte
- Erstelle eine kurze Status-Zusammenfassung

### 3. Fehler-Analyse
- Pruefe ob Agents Fehler in ihren Memory-Logs reportet haben
- Kategorisiere: CRITICAL (sofort handeln), WARNING (beobachten), INFO (dokumentieren)
- Bei CRITICAL: Erstelle einen Eintrag in deiner `memory/YYYY-MM-DD.md` mit Handlungsempfehlung

### 4. Memory-Hygiene
- Pruefe ob MEMORY.md aktuell ist (letzte Aenderung < 7 Tage)
- Konsolidiere Erkenntnisse aus Daily Logs in MEMORY.md
- Entferne veraltete Eintraege


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Keine externen API-Calls ohne explizite Freigabe
- Keine Aenderungen an SOUL.md-Dateien anderer Agents
- Nur lesender Zugriff auf andere Agent-Workspaces
- Aenderungen an Konfigurationsdateien nur nach Dokumentation in Memory
- Bei unbekannten Fehlern: Dokumentieren und HEARTBEAT_OK — NICHT autonom fixen
