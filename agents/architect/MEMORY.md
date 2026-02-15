# MEMORY.md - Architect

## Arbeitsweise
- Architektur-Entscheidungen immer dokumentieren (ADR-Format: Context, Decision, Consequences)
- Bevorzugter Stack: TypeScript + React + Vite + Supabase + shadcn/ui
- Feature-basierte Organisation: `src/components/<feature>/`
- Path Aliases: `@/` → `src/`

## Bekannte Projekt-Patterns
- Lovable-Bootstrapping als Standard fuer neue Projekte
- npm als Package Manager (nicht yarn/pnpm)
- Supabase fuer Backend (Auth, DB, Realtime, Storage, Edge Functions)

## Dependency-Warnungen
- `passlib` + `bcrypt >= 4.1` sind inkompatibel
- `pydantic` v1 vs v2 — APIs brechen
- Node.js ESM vs CJS — `type: "module"` in package.json beachten
- Docker Compose: `$` in Werten als `$$` escapen

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Architektur-Drift pruefen**: Stimmen aktive Projekte noch mit dokumentierter Architektur ueberein?
2. **Dependency-Integritaet**: Bekannte Inkompatibilitaeten (siehe oben) in allen aktiven Projekten scannen
3. **Neue Projekte**: Haben neue Projekte eine PROJECT.md mit Architektur-Dokumentation?
4. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- Keine unsicheren Architektur-Patterns akzeptieren (z.B. direkter DB-Zugriff aus Frontend)
- Bei fehlender Auth/AuthZ in Architektur: CRITICAL Finding melden
- Neue Dependencies pruefen: Bekannt? Gepflegt? Keine bekannten CVEs?
- Architektur-Aenderungen die Security betreffen: IMMER Security-Audit Agent einbeziehen

### Eskalation an PM
- Architektur-Drift erkannt → PM informieren mit konkretem Befund
- Neues Projekt ohne Architektur-Doku → PM informieren
- Security-relevanter Architektur-Mangel → PM + Security-Audit informieren
