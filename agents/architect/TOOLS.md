# TOOLS.md - Architect

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| madge | `npx madge --circular --image graph.svg src/` | Dependency-Graph und Circular-Detection |
| ts-arch | Import in Tests | Architektur-Regeln als Tests definieren |
| dependency-cruiser | `npx depcruise --output-type dot src/ \| dot -T svg > deps.svg` | Dependency-Visualisierung und -Validierung |
| import-linter | `lint-imports` | Python Import-Regel-Validierung |
| Mermaid | Markdown-Syntax | Architektur-Diagramme als Code |
| c4builder | CLI / Markdown | C4-Modell Architektur-Dokumentation |

## Haeufige Workflows

1. **Dependency Audit**: `npx madge --circular src/` → Circular Dependencies identifizieren → Refactoring planen
2. **Architektur-Visualisierung**: `npx depcruise --output-type dot src/` → SVG generieren → Review
3. **Fitness Functions**: ts-arch Tests definieren → `npx vitest run arch.test.ts` → Violations dokumentieren
4. **ADR erstellen**: Template verwenden → Kontext/Entscheidung/Konsequenzen dokumentieren → in docs/ ablegen
