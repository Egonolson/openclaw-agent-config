# TOOLS.md - Security Audit

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| Semgrep | `semgrep scan --config auto .` | SAST — statische Pattern-basierte Code-Analyse |
| CodeQL | `codeql database analyze` | Tiefgehende semantische Code-Analyse (GitHub-integriert) |
| Bandit | `bandit -r src/` | Python-spezifische Security-Analyse |
| trufflehog | `trufflehog git file://. --since-commit HEAD~10` | Secret-Detection in Git-History |
| trivy | `trivy fs --severity HIGH,CRITICAL .` | Vulnerability-Scanner fuer Filesystem und Dependencies |
| SARIF-Parser | JSON-basiertes Ergebnis-Format | Standardisiertes Format fuer Security-Findings |

## Haeufige Workflows

1. **Full Security Scan**: `semgrep scan --config auto . --sarif -o results.sarif` → SARIF parsen → Findings nach Severity sortieren
2. **Secret Leak Check**: `trufflehog git file://. --since-commit HEAD~10` → Findings dokumentieren → betroffene Commits identifizieren
3. **Dependency Audit**: `trivy fs --severity HIGH,CRITICAL .` → CVE-IDs gegen NVD pruefen → Upgrade-Empfehlungen erstellen
4. **Variant Analysis**: Finding identifizieren → Semgrep-Custom-Rule erstellen → auf gesamte Codebase anwenden
