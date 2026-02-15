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

## Mobile-First Konventionen (PFLICHT)
- Bottom Navigation Bar fuer Mobile-Hauptnavigation (max 5 Items, Icons + Labels)
- Bottom Sheets statt zentrierte Modals (Vaul/shadcn Sheet side="bottom")
- Icon Buttons bevorzugen (Lucide, 44x44px Touch-Target, Tooltip nur Desktop)
- Swipe-Gesten: Slide Navigation, Swipe-Actions auf Listen, Pull-to-Refresh
- FAB fuer primaere Erstellungs-Aktion (Bottom-Right, ueber Bottom Nav)
- Safe Area Insets beachten (Notch, Home Indicator)
- Breakpoints: <640px Single Column + Bottom Nav | 640-1024px Optional Sidebar | >1024px Full Layout
- KEIN Hover-only Content — alles muss touch-erreichbar sein

## Session & Error UI-Konventionen
- "Angemeldet bleiben" Checkbox auf Login (default an)
- Sanfter Session-Ablauf mit Re-Login-Overlay (kein 401 Error)
- Return-URL nach Re-Login — User kommt zurueck wo er war
- Error Boundary bei Crashes (Retry-Button, kein White Screen)
- Toast fuer API-Fehler, Inline-Fehler bei Formularen
- Offline-Banner bei Netzwerkverlust

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
