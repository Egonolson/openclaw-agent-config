# HEARTBEAT.md - Prototyper

## Automated Health Checks

### 1. Prototyp-Status
- Pruefe ob aktive Prototypen lauffaehig sind (Dependency-Checks, Build-Status)
- Identifiziere Prototypen die seit > 48h keine Aktivitaet zeigen
- Dokumentiere Status in `memory/YYYY-MM-DD.md`

### 2. Dependency-Gesundheit
- Pruefe `package.json` / `requirements.txt` auf bekannte Konflikte
- Bekannte Issues: passlib+bcrypt>=4.1, pydantic v1/v2, ESM/CJS Konflikte
- Melde veraltete Lock-Files

### 3. Memory-Hygiene
- Konsolidiere Prototyp-Erkenntnisse in MEMORY.md
- Dokumentiere wiederverwendbare Patterns und Boilerplate-Templates

### 4. Senior-Expert-Checks
- **Prototyp-Lifecycle**: Prototypen die > 14 Tage alt sind als "Uebergangs-Kandidat" markieren
- **Lock-File Integritaet**: package-lock.json/requirements.txt Hash validieren


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Keine Code-Aenderungen im Heartbeat — nur Analyse
- Keine externen Package-Installationen
- Keine Docker-Operationen (an Deploy-Agent delegieren)
- Bei unsicheren Dependencies: Dokumentieren und melden