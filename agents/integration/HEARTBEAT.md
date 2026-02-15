# HEARTBEAT.md - Integration

## Automated Health Checks

### 1. Integration-Health
- Pruefe API-Erreichbarkeit aller aktiven Integrationen (HTTP GET auf Health/Status-Endpoints)
- Validiere OAuth-Token-Gueltigkeit (Expiry-Time pruefen, Refresh wenn < 1h)
- Pruefe Webhook-Registrierungen auf aktiven Status
- Dokumentiere Ergebnisse in `memory/YYYY-MM-DD.md`

### 2. n8n Workflow Status
- Pruefe Failed Executions der letzten 24h (`mcp__n8nvision-mcp__n8n_executions`)
- Identifiziere Workflows mit erhoehter Fehlerrate
- Schema-Drift Detection: Input-Payloads gegen erwartete Schemas validieren
- Bei CRITICAL (Workflow komplett down): Sofort melden

### 3. Supabase Edge Function Health
- Error-Rate der Edge Functions pruefen (`mcp__supabasevisionx__get_logs`)
- Cold-Start-Zeiten dokumentieren und Baselines vergleichen
- Memory-Usage und Execution-Time monitoren
- Bei Error-Rate > 5%: WARNING loggen

### 4. Schema-Drift Detection
- Field-Mappings gegen aktuelle API-Responses validieren
- Neue/entfernte Felder in API-Responses erkennen
- Breaking Changes in Webhook-Payloads identifizieren
- Drift-Report in Memory dokumentieren

### 5. Senior-Expert-Checks
- **API Rate-Limit Monitoring**: Verbrauch pro Integration tracken, Warnung bei > 80% Limit
- **Webhook Delivery Health**: Delivery-Success-Rate pro Webhook, Failed Deliveries identifizieren
- **OAuth Token Expiry Warnings**: Tokens die in < 24h ablaufen proaktiv melden
- **Circuit-Breaker Status**: Offene Circuit-Breaker identifizieren und Recovery pruefen
- **Field Mapping Coverage**: Unmapped Fields in Source/Target erkennen


## Fehlerbehandlung

Alle erkannten Fehler werden SOFORT in diesem Heartbeat behandelt.
Bearbeitungsreihenfolge: CRITICAL → HIGH → MEDIUM → LOW
Kein Fehler wird auf den naechsten Heartbeat verschoben.

### Security-Vorgaben
- Keine Full-Tokens in Logs — nur letzte 4 Zeichen anzeigen (`...xxxx`)
- HMAC-Validation fuer alle eingehenden Webhooks pruefen
- HTTPS-only — keine unverschluesselten API-Calls
- Rate-Limiting der Ziel-APIs respektieren — nie mehr als erlaubt anfragen
- Bei Verdacht auf Token-Leak: Sofort melden, Token rotieren lassen
- Credentials nur aus sicheren Stores lesen (ENV, Vault), nie hardcoded
