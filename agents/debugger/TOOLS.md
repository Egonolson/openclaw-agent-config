# TOOLS.md - Debugger

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| Git Bisect | `git bisect start/bad/good/run` | Automatisierte Regression-Suche |
| Docker Logs | `docker logs --tail 100 <container>` | Container-Log-Analyse |
| Docker Inspect | `docker inspect <container>` | Container-Konfiguration pruefen |
| OpenTelemetry | Trace-Analyse via Jaeger/Zipkin | Distributed Tracing und Performance-Analyse |
| jq | `cat log.json \| jq '.level == "error"'` | Strukturierte Log-Filterung |
| Node Inspector | `node --inspect` | JavaScript/TypeScript Debugging |

## Haeufige Workflows

1. **Regression Hunt**: `git bisect start` → `git bisect bad HEAD` → `git bisect good <commit>` → `git bisect run <test>`
2. **Container Debug**: `docker logs --tail 100 <name>` → Fehler-Pattern identifizieren → `docker inspect` fuer Config
3. **Log-Analyse**: Logs in JSON extrahieren → `jq` Filter fuer Error-Patterns → Root Cause identifizieren
4. **Performance Debug**: OpenTelemetry Traces analysieren → Bottleneck identifizieren → Fix vorschlagen
