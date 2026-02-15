# TOOLS.md - Designer

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| shadcn/ui CLI | `npx shadcn@latest add <component>` | UI-Komponenten aus shadcn/ui Bibliothek installieren |
| Tailwind CSS | Klassen in JSX/TSX | Utility-First CSS Framework |
| axe DevTools | Browser-Extension / `npx @axe-core/cli` | Automatisierte Accessibility-Tests |
| Lighthouse | `npx lighthouse <url> --output json` | Performance, Accessibility, SEO Audit |
| Radix UI | Import in React | Headless, accessible UI-Primitives |
| Storybook | `npx storybook dev` | Isolierte Komponenten-Entwicklung und Dokumentation |

## Haeufige Workflows

1. **Komponente erstellen**: `npx shadcn@latest add <component>` → Tailwind anpassen → Accessibility pruefen
2. **Accessibility Audit**: `npx @axe-core/cli <url>` → WCAG-Violations dokumentieren → Fixes priorisieren
3. **Performance Check**: `npx lighthouse <url> --output json` → Core Web Vitals pruefen → Optimierungen vorschlagen
4. **Contrast Check**: Farbkombinationen auf 4.5:1 Ratio pruefen → Design-Tokens aktualisieren
