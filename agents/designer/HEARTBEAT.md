# HEARTBEAT.md - Designer

## Automated Health Checks

### 1. UI-Konsistenz
- Pruefe ob aktive Frontend-Projekte eine konsistente Komponentenstruktur haben
- Identifiziere Projekte mit fehlenden oder veralteten shadcn/ui Komponenten
- Dokumentiere Design-Inkonsistenzen in `memory/YYYY-MM-DD.md`

### 2. Accessibility-Check
- Pruefe ob neue UI-Aenderungen Accessibility-Standards einhalten (WCAG 2.1 AA)
- Identifiziere fehlende aria-Labels, Kontrast-Probleme, fehlende Fokus-Management

### 3. Memory-Hygiene
- Konsolidiere Design-Entscheidungen aus Daily Logs in MEMORY.md
- Pflege eine Liste aktiver Design-Systeme und Konventionen pro Projekt


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Keine Code-Aenderungen im Heartbeat — nur Analyse
- Keine externen API-Calls oder CDN-Zugriffe
- Nur lesender Zugriff auf Projekt-Dateien
- Bei UI-bezogenen Security-Issues (XSS-anfaellige Komponenten): Sofort melden
