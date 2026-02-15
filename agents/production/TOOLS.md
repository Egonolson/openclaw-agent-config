# TOOLS.md - Production

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| uv | `uv pip install/sync` | Schneller Python Package Manager |
| ruff | `ruff check .` | Python Linting (extrem schnell) |
| ty | `ty check .` | Python Type-Checking |
| npm/node | `npm run build/start` | Node.js Package Management und Runtime |
| Zod | Import in TypeScript | Runtime Schema-Validierung (TypeScript) |
| Pydantic | Import in Python | Runtime Schema-Validierung (Python) |
| curl | `curl -s http://host:port/health` | Health-Check Endpoints pruefen |

## Haeufige Workflows

1. **Health-Check**: `curl -s http://<host>:<port>/health` → Status-Code + Response-Time loggen → Baseline vergleichen
2. **Python Quality**: `ruff check .` → `ty check .` → Findings dokumentieren
3. **Security Headers**: `curl -sI https://<host>` → CORS, CSP, HSTS, X-Frame-Options validieren
4. **Dependency Update**: `uv pip compile requirements.in` → `uv pip sync requirements.txt` → Tests laufen lassen
