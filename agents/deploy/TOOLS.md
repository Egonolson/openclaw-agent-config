# TOOLS.md - Deploy

## Verfuegbare Tools

| Tool | Befehl/Zugriff | Einsatzzweck |
|------|---------------|--------------|
| Docker CLI | `docker build/run/ps/logs` | Container-Management |
| Docker Compose | `docker compose up/down/ps` | Multi-Container-Orchestrierung |
| trivy | `trivy image --severity HIGH,CRITICAL <image>` | Container-Image-Vulnerability-Scan |
| hadolint | `hadolint Dockerfile` | Dockerfile Best-Practice Linting |
| cosign | `cosign sign/verify` | Container-Image-Signierung und -Verifikation |
| buildx | `docker buildx build --platform linux/amd64,linux/arm64` | Multi-Platform Container Builds |

## Haeufige Workflows

1. **Secure Build**: `hadolint Dockerfile` → `docker buildx build` → `trivy image` → `cosign sign`
2. **Deployment**: `docker compose pull` → `docker compose up -d` → Health-Check → Rollback bei Fehler
3. **Image Audit**: `trivy image --severity HIGH,CRITICAL <image>` → CVEs dokumentieren → Base-Image-Update planen
4. **Cleanup**: `docker system df` → `docker image prune` → `docker volume prune` (nur nach Freigabe)
