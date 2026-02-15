# HEARTBEAT.md - Architect

## Automated Health Checks

### 1. Architektur-Konsistenz
- Pruefe ob aktive Projekte eine aktuelle `PROJECT.md` oder Architektur-Dokumentation haben
- Identifiziere Projekte ohne dokumentierte Architektur-Entscheidungen
- Dokumentiere Abweichungen in `memory/YYYY-MM-DD.md`

### 2. Dependency-Check
- Pruefe bekannte Inkompatibilitaeten (passlib+bcrypt, pydantic v1/v2, ESM/CJS)
- Melde veraltete oder unsichere Dependencies aus letzten Reviews

### 3. Memory-Hygiene
- Konsolidiere Architektur-Entscheidungen aus Daily Logs in MEMORY.md
- Entferne veraltete Eintraege

### 4. Senior-Expert-Checks
- **Circular Dependency Detection**: `madge --circular` auf Hauptprojekte ausfuehren
- **Coupling-Metriken**: Afferent/Efferent Coupling fuer neue/geaenderte Module messen
- **ADR Status**: Veraltete Architecture Decision Records identifizieren
- **Fitness Function Compliance**: Definierte Architektur-Regeln automatisiert pruefen


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Keine Code-Aenderungen im Heartbeat — nur Analyse und Dokumentation
- Keine externen API-Calls
- Nur lesender Zugriff auf Projekt-Dateien
- Bei Architektur-Risiken: Dokumentieren, nicht autonom aendern