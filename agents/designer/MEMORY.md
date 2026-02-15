# MEMORY.md - Designer

## Design-System Konventionen
- shadcn/ui als Komponenten-Bibliothek
- Tailwind CSS fuer Styling (keine CSS-Module, kein styled-components)
- Radix UI Primitives als Basis
- Lucide Icons als Icon-Library
- `cn()` Utility aus `@/lib/utils` fuer className-Merging
- Varianten ueber `cva()` (class-variance-authority)

## UX-Standards
- WCAG 2.1 AA als Mindeststandard
- Mobile-first Responsive Design
- Skeleton Loader statt Spinner
- Labels ueber dem Input (nicht als Placeholder)
- Transitions: 150-300ms, ease-out

## Aktive Design-Systeme
_(Pro Projekt — gepflegt durch Heartbeat)_

## Design-Entscheidungen
_(Archiv wichtiger Design-Entscheidungen und deren Begruendung)_

---

## DAUERHAFTE PFLICHT-REVIEWS (PM-orchestriert)

### Bei jedem Heartbeat
1. **Komponentenstruktur**: Aktive Frontend-Projekte auf konsistente Organisation pruefen
2. **Accessibility-Status**: Bekannte A11y-Issues tracken und nachverfolgen
3. **Design-Konsistenz**: Pruefen ob neue UI-Aenderungen dem Design-System folgen
4. **Ergebnisse in `memory/YYYY-MM-DD.md` loggen** — PM liest diese im naechsten Heartbeat

### Security-Pflichtpunkte (immer ausfuehren)
- **XSS-Prevention**: UI-Komponenten auf unsichere Raw-HTML-Injection pruefen (z.B. unsanitized innerHTML)
- **Input-Sanitization**: Formular-Eingaben muessen sanitized werden bevor sie angezeigt werden
- **CSP-Kompatibilitaet**: Keine inline-Styles oder -Scripts die CSP brechen wuerden
- **Link-Security**: Externe Links mit `rel="noopener noreferrer"` versehen
- Bei Security-relevanten UI-Findings → PM + Security-Audit informieren

### Eskalation an PM
- Grobe Design-Inkonsistenz in Production-Projekt → PM informieren
- A11y-Verstoesse die User betreffen → PM informieren
- XSS-anfaellige Komponenten gefunden → PM + Security-Audit informieren
