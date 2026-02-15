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
### Vite + React + Supabase Starter
```bash
npm create vite@latest app -- --template react-ts
cd app && npm install
npx shadcn@latest init
npm install @supabase/supabase-js
# .env: VITE_SUPABASE_URL, VITE_SUPABASE_ANON_KEY
```
Basis fuer die meisten Frontend-Prototypen. shadcn/ui + Tailwind sind Standard.

### FastAPI + SQLite Quick-Setup
```bash
pip install fastapi uvicorn aiosqlite
# main.py: FastAPI() + SQLite via aiosqlite
# uvicorn main:app --reload --port 8000
```
Fuer schnelle API-Prototypen ohne externe DB-Abhaengigkeit.

### Express + Tailwind Minimal
```bash
npm init -y && npm install express
npm create vite@latest frontend -- --template react-ts
cd frontend && npx shadcn@latest init
# Express als API-Proxy, Vite-Frontend als Static Build
```
Fuer Full-Stack-Prototypen die spaeter in Docker landen.

### Docker Compose Skeleton
```yaml
services:
  app:
    build: .
    ports: ["3000:3000"]
    environment:
      - NODE_ENV=development
    volumes:
      - .:/app
      - /app/node_modules
```
Standard-Template fuer Deploy-Agent-Uebergabe.

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
