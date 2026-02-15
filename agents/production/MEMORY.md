# MEMORY.md - Production

## Monitoring-Konventionen
- Health-Checks via HTTP GET auf `/health` Endpoint
- Response-Time Baseline dokumentieren fuer jedes Service
- Fehler-Kategorisierung: CRITICAL (down), WARNING (degradiert), INFO (einzeln)

## Feature-Pipeline-Konventionen
- Code wird IMMER durch code-audit reviewed — schreibe review-ready Code
- Unit Tests sind Pflicht fuer jede Business-Logik
- Nach deiner Arbeit kommt: Code Review → Security Review → Test → Deploy → E2E
- Keine Shortcuts: Kein "das fixe ich spaeter" oder "Tests kommen noch"
- Mobile-First: Responsive Design ist Pflicht — Bottom Nav, Bottom Sheets, Icon Buttons, Touch-Targets
- Session Management: Persistente Sessions PFLICHT bei Login-Apps (Refresh Tokens, HTTP-Only Cookies, Auto-Refresh)
- Supabase: `persistSession: true` + `onAuthStateChange` Listener — Session bleibt erhalten
- Error Logging: Structured JSON Logs mit Correlation ID, Error Boundary im Frontend, Severity Levels
- Keine sensiblen Daten in Logs (Passwort, Token, PII)

## Bekannte Services
_(Wird durch Heartbeat gepflegt — Service-URLs, erwartete Ports, Baselines)_

## Incident-Log
_(Wird durch Heartbeat gepflegt — Service-Ausfaelle, Recovery-Aktionen)_

## Performance-Baselines
_(Response-Times und Verfuegbarkeit — gepflegt durch Heartbeat)_

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Service-Health**: HTTP GET auf jeden bekannten `/health` Endpoint — Status + Response-Time
2. **Error-Rate**: Application-Logs auf 5xx, ECONNREFUSED, OOM, Timeout scannen
3. **Performance-Drift**: Response-Times mit Baselines vergleichen — > 200% = WARNING
4. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- **Nur GET-Requests**: Health-Checks duerfen NIE den Zustand aendern
- **Token-Maskierung**: URLs mit Auth-Tokens in Logs maskieren
- **Rate-Limiting**: Max 1 Health-Check pro Service pro Heartbeat
- **Anomalie-Erkennung**: Unerwartete Endpoints, offene Ports, neue Services melden
- **SSL/TLS**: HTTPS-Endpoints auf Zertifikats-Gueltigkeit pruefen
- Bei Security-Anomalien: SOFORT PM + Security-Audit informieren

### Auto-Recovery Regeln
- Service unresponsive: 3x Retry mit 10s Pause
- Nach 3 Failures: Deploy-Agent informieren (NICHT selbst restarten)
- Dokumentiere JEDEN Recovery-Versuch im Incident-Log

### Eskalation an PM
- Service down > 5min → PM sofort informieren
- Performance-Degradation > 200% → PM informieren
- Neuer/unbekannter Service erkannt → PM + Security-Audit informieren
- SSL-Zertifikat laeuft ab < 14 Tage → PM informieren
