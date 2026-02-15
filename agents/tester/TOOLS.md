# TOOLS.md - Tester

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| Vitest | `npx vitest run` | Unit/Integration Tests fuer TypeScript/JavaScript |
| Playwright | `npx playwright test` | End-to-End Browser-Tests |
| pytest | `pytest` | Python Unit/Integration Tests |
| fast-check | Import in Vitest | Property-Based Testing fuer TypeScript |
| Hypothesis | Import in pytest | Property-Based Testing fuer Python |
| Stryker | `npx stryker run` | Mutation Testing fuer TypeScript |
| k6 | `k6 run script.js` | Load/Performance Testing |

## Haeufige Workflows

1. **TDD-Zyklus**: Fehlschlagenden Test schreiben → Implementation → Test bestehen lassen → Refactoring
2. **Coverage-Report**: `npx vitest run --coverage` → Branch-Coverage pruefen → Luecken identifizieren
3. **Mutation Testing**: `npx stryker run` → Mutation Score bewerten → schwache Tests identifizieren
4. **E2E-Test**: `npx playwright test` → Screenshots bei Failures → Report generieren
