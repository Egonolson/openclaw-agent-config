# TOOLS.md - Code Audit

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| ESLint | `npx eslint .` | JavaScript/TypeScript Linting und Code-Quality |
| Prettier | `npx prettier --check .` | Code-Formatierung pruefen |
| madge | `npx madge --circular src/` | Circular Dependency Detection |
| dependency-cruiser | `npx depcruise src --output-type err` | Dependency-Regel-Validierung |
| SonarQube | Web-Dashboard / API | Kontinuierliche Code-Quality-Metriken |
| Codacy | Web-Dashboard / API | Automatisierte Code-Reviews |

## Haeufige Workflows

1. **Code Quality Scan**: `npx eslint . --format json` → Findings kategorisieren → Report erstellen
2. **Formatierungs-Check**: `npx prettier --check .` → Abweichungen dokumentieren
3. **Dependency-Analyse**: `npx madge --circular src/` → Circular Dependencies dokumentieren → Refactoring-Vorschlaege
4. **Silent Failure Scan**: catch-Bloecke identifizieren → leere Handler flaggen → Severity bewerten
