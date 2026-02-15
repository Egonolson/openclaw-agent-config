# MEMORY.md - Integration

## Integration-Konventionen
- Credentials werden IMMER als Environment Variables gespeichert, nie im Code
- Token-Storage: Supabase Vault fuer Production, `.env` fuer Development
- Retry-Logic: Exponential Backoff (1s, 2s, 4s, 8s, max 16s), max 5 Retries
- Alle API-Calls muessen Timeouts haben (Default: 10s connect, 30s read)
- Jede Integration braucht einen Health-Check Endpoint

## Wiederverwendbare Patterns

### Supabase Edge Function Template (OAuth Token Refresh)
```typescript
// Pattern: OAuth Token Refresh in Edge Function
import { createClient } from "@supabase/supabase-js";

Deno.serve(async (req) => {
  const supabase = createClient(
    Deno.env.get("SUPABASE_URL")!,
    Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!
  );

  // Token aus Vault laden
  const { data: token } = await supabase
    .from("integration_tokens")
    .select("access_token, refresh_token, expires_at")
    .eq("provider", "example")
    .single();

  // Refresh wenn < 5min gueltig
  if (new Date(token.expires_at) < new Date(Date.now() + 5 * 60 * 1000)) {
    const refreshed = await refreshOAuthToken(token.refresh_token);
    await supabase
      .from("integration_tokens")
      .update({
        access_token: refreshed.access_token,
        expires_at: refreshed.expires_at,
      })
      .eq("provider", "example");
  }

  return new Response(JSON.stringify({ status: "ok" }), {
    headers: { "Content-Type": "application/json" },
  });
});
```

### n8n Webhook Validation Node Template
```javascript
// Pattern: HMAC Webhook Validation (n8n Code Node)
const crypto = require("crypto");

const secret = $env.WEBHOOK_SECRET;
const signature = $input.first().headers["x-hub-signature-256"];
const payload = JSON.stringify($input.first().body);

const expected =
  "sha256=" + crypto.createHmac("sha256", secret).update(payload).digest("hex");

if (signature !== expected) {
  throw new Error("Invalid webhook signature");
}

return $input.all();
```

### Field Mapping JSON Schema Template
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "source_field": {
      "type": "string",
      "description": "Feldname im Source-System"
    },
    "target_field": {
      "type": "string",
      "description": "Feldname im Target-System"
    },
    "transform": {
      "type": "string",
      "enum": ["direct", "uppercase", "lowercase", "date_iso", "custom"],
      "description": "Transformationsregel"
    },
    "required": {
      "type": "boolean",
      "default": true
    }
  },
  "required": ["source_field", "target_field", "transform"]
}
```

### OAuth 2.0 Flow Implementation (TypeScript)
```typescript
// Pattern: OAuth 2.0 Authorization Code Flow
interface OAuthConfig {
  clientId: string;
  clientSecret: string;
  tokenUrl: string;
  scopes: string[];
}

async function refreshOAuthToken(
  config: OAuthConfig,
  refreshToken: string
): Promise<{ access_token: string; expires_at: string }> {
  const response = await fetch(config.tokenUrl, {
    method: "POST",
    headers: { "Content-Type": "application/x-www-form-urlencoded" },
    body: new URLSearchParams({
      grant_type: "refresh_token",
      client_id: config.clientId,
      client_secret: config.clientSecret,
      refresh_token: refreshToken,
    }),
  });

  if (!response.ok) {
    throw new Error(`Token refresh failed: ${response.status}`);
  }

  const data = await response.json();
  return {
    access_token: data.access_token,
    expires_at: new Date(Date.now() + data.expires_in * 1000).toISOString(),
  };
}
```

### API Circuit Breaker Pattern
```typescript
// Pattern: Simple Circuit Breaker
class CircuitBreaker {
  private failures = 0;
  private lastFailure = 0;
  private state: "closed" | "open" | "half-open" = "closed";

  constructor(
    private threshold: number = 5,
    private resetTimeout: number = 30000
  ) {}

  async execute<T>(fn: () => Promise<T>): Promise<T> {
    if (this.state === "open") {
      if (Date.now() - this.lastFailure > this.resetTimeout) {
        this.state = "half-open";
      } else {
        throw new Error("Circuit breaker is open");
      }
    }

    try {
      const result = await fn();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }

  private onSuccess() {
    this.failures = 0;
    this.state = "closed";
  }

  private onFailure() {
    this.failures++;
    this.lastFailure = Date.now();
    if (this.failures >= this.threshold) {
      this.state = "open";
    }
  }
}
```

## Bekannte Integration-Probleme
- **Rate-Limiting**: Verschiedene APIs haben unterschiedliche Limits — immer Response-Header `X-RateLimit-Remaining` pruefen
- **Webhook Replay**: Idempotency-Keys verwenden, Replay-Detection via Timestamp + Nonce
- **Schema Evolution**: APIs koennen Felder hinzufuegen — strenge Validierung vermeiden, nur required Fields pruefen
- **Token Expiry in n8n**: n8n Credential-Store refresht nicht automatisch — Custom Refresh-Logic in Code Nodes noetig
- **Cold Starts**: Supabase Edge Functions haben Cold-Start-Latenz von 100-500ms — fuer zeitkritische Webhooks beruecksichtigen

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Integration-Health**: Erreichbarkeit aller aktiven API-Endpoints pruefen — Status + Response-Time
2. **Token-Status**: OAuth-Tokens auf Gueltigkeit pruefen — ablaufende Tokens melden
3. **Webhook-Health**: Delivery-Success-Rate pruefen — Failed Deliveries identifizieren
4. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- **Token-Maskierung**: Credentials in Logs IMMER maskieren (nur letzte 4 Zeichen)
- **HMAC-Validation**: Eingehende Webhooks IMMER auf gueltige Signatur pruefen
- **HTTPS-Only**: Keine unverschluesselten Verbindungen — Violations sofort melden
- **Rate-Limit-Respekt**: API-Limits nie ueberschreiten — bei > 80% Verbrauch warnen
- Bei Security-Anomalien: SOFORT PM + Security-Audit informieren

### Eskalation an PM
- API-Integration down > 5min → PM sofort informieren
- OAuth-Token laeuft ab < 24h → PM informieren
- Webhook-Delivery-Rate < 95% → PM informieren
- Schema-Drift erkannt (Breaking Change) → PM + betroffene Agents informieren
- Rate-Limit erreicht → PM informieren, betroffene Workflows pausieren
