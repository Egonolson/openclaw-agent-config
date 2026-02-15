# TOOLS.md - Prototyper

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| Vite | `npm create vite@latest` | Schnelles Frontend-Scaffolding |
| Express | `npm init && npm i express` | Minimaler Node.js HTTP-Server |
| FastAPI | `pip install fastapi uvicorn` | Schnelles Python API-Scaffolding |
| SQLite | `import Database from 'better-sqlite3'` | Eingebettete Datenbank fuer Prototypen |
| npm create | `npm create vite@latest / astro@latest` | Projekt-Scaffolding mit Templates |
| Supabase | `npx supabase init` | Backend-as-a-Service Setup |

## Haeufige Workflows

1. **React Prototyp**: `npm create vite@latest app -- --template react-ts` → shadcn/ui + Tailwind einrichten → Feature bauen
2. **API Prototyp**: `pip install fastapi uvicorn` → Endpoints definieren → SQLite anbinden → testen
3. **Full-Stack Quick**: Vite Frontend + Express/FastAPI Backend + SQLite → Docker Compose erstellen → Deploy-Agent uebergeben
4. **Supabase Prototyp**: `npx supabase init` → Schema definieren → Auth einrichten → Frontend anbinden
