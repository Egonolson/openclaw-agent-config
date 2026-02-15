# TOOLS.md - Integration

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| n8n MCP | `mcp__n8nvision-mcp__*` | Workflow-Erstellung, Validierung, Deployment |
| Supabase MCP | `mcp__supabasevisionx__*` | Edge Functions, Migrations, SQL-Ausfuehrung |
| GitHub MCP | `mcp__github__*` | Integration-Config versionieren, PRs erstellen |
| curl / jq | `curl -s <url> \| jq '.'` | API-Testing, JSON-Transformation, Health-Checks |
| openapi-generator | `npx @openapitools/openapi-generator-cli generate` | TS-Client aus OpenAPI-Spec generieren |
| ajv-cli | `npx ajv validate -s schema.json -d data.json` | JSON Schema Validation fuer Field-Mappings |

## Haeufige Workflows

1. **API-Integration Setup**: OpenAPI-Spec laden → Client generieren → Typen pruefen → Credentials konfigurieren → Smoke-Test
2. **n8n Workflow Deploy**: Workflow erstellen → Validieren → Testen → Deployen → Health-Check einrichten
3. **Edge Function Deploy**: Function schreiben → Lokal testen → Supabase deployen → Logs pruefen → Monitoring einrichten
4. **Field Mapping**: Source-Schema laden → Target-Schema laden → Mapping definieren → JSON Schema validieren → Transformations-Tests
5. **Webhook Integration**: Endpoint registrieren → HMAC-Secret konfigurieren → Payload-Validation implementieren → Retry-Logic testen
6. **OAuth Flow**: Client-Credentials konfigurieren → Token-Endpoint testen → Refresh-Logic implementieren → Token-Storage sichern
