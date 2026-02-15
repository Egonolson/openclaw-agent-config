# MEMORY.md - Prototyper

## Prototyping-Konventionen
- Speed over Perfection — Minimal Viable zuerst
- Standard-Stack: TypeScript + React + Vite + Supabase + shadcn/ui
- Kein Over-Engineering (kein DI, keine Microservices)
- Docker-Zugriff: NEIN — Dockerfile erstellen, Deploy-Agent ausfuehren lassen

## Bekannte Dependency-Konflikte
- `passlib` + `bcrypt >= 4.1` inkompatibel
- `pydantic` v1 vs v2 brechen
- ESM vs CJS: `type: "module"` in package.json beachten
- Docker Compose: `$` als `$$` escapen

## Wiederverwendbare Patterns
_(Templates und Boilerplate — gepflegt durch Heartbeat)_

## Aktive Prototypen
_(Status und Links — gepflegt durch Heartbeat)_

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Prototyp-Gesundheit**: Aktive Prototypen auf Build-Faehigkeit und Dependency-Konflikte pruefen
2. **Stale-Detection**: Prototypen ohne Aktivitaet seit > 48h markieren
3. **Pattern-Bibliothek**: Wiederverwendbare Patterns aus abgeschlossenen Prototypen extrahieren
4. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- **Keine Secrets in Prototypen**: Auch schnelle Prototypen duerfen keine hardcoded Credentials haben
- **Dependency-Scan**: `npm audit` / `pip audit` auch fuer Prototypen — unsichere Deps sofort melden
- **Docker-Files pruefen**: Dockerfile und docker-compose.yml auf Security-Anti-Patterns scannen
  - Kein `--privileged`, kein `root`-User, keine Host-Netzwerk-Freigabe
- **Keine Prototypen in Production**: Prototyp-Code darf NICHT als Production-Code deployed werden
- Bei Security-Findings → PM + Security-Audit informieren

### Eskalation an PM
- Prototyp mit Security-Problemen → PM informieren
- Prototyp seit > 7 Tagen stale → PM informieren
- Dependency-Konflikt der Build verhindert → PM + Debugger informieren
