# OpenClaw Agent-Konfiguration

Komplette Dokumentation und Referenz-Configs fuer ein Multi-Agent OpenClaw Setup mit Docker-Sandboxes, MCP-Servern (Context7, Playwright) und Host-Networking.

## Verzeichnisstruktur

```
~/.openclaw/                          # Hauptverzeichnis
├── openclaw.json                     # Zentrale Config (manuell bearbeiten)
├── config/
│   ├── mcporter.json                 # MCP-Server fuer Gateway
│   └── sandbox-tester/
│       └── mcporter.json             # MCP-Server fuer Tester-Sandbox
├── workspace/                        # Geteilter Workspace (alle Agents)
└── workspaces/                       # Individuelle Agent-Workspaces
    ├── project-manager/
    ├── deploy/
    ├── prototyper/
    ├── production/
    ├── security-audit/
    ├── code-audit/
    ├── architect/
    ├── tester/
    ├── designer/
    └── debugger/
```

Die **Workspace-Ordner** werden automatisch vom Onboarding-Wizard erstellt. Der `config/`-Ordner wird manuell angelegt.

---

## Agenten-Uebersicht

| # | Agent | ID | Avatar | Mention-Trigger | Sandbox? |
|---|-------|----|--------|-----------------|----------|
| 1 | **Project Manager** | `project-manager` | `📋` | `@pm` `@projektmanager` | Nein |
| 2 | **Deploy & Docker** | `deploy` | `🐳` | `@deploy` `@docker` | **Ja** (eigenes Image) |
| 3 | **Prototyper** | `prototyper` | `⚡` | `@prototyper` `@proto` | Nein |
| 4 | **Production Engineer** | `production` | `🏭` | `@production` `@prod` | Nein |
| 5 | **Security Auditor** | `security-audit` | `🔒` | `@security` | Nein |
| 6 | **Code Reviewer** | `code-audit` | `🔍` | `@code-audit` `@review` | Nein |
| 7 | **System Architect** | `architect` | `🏗️` | `@architect` `@arch` | Nein |
| 8 | **QA & Test Engineer** | `tester` | `🧪` | `@tester` `@test` `@qa` | **Ja** (Playwright) |
| 9 | **UI/UX Designer** | `designer` | `🎨` | `@designer` `@ui` `@ux` | Nein |
| 10 | **Debugger** | `debugger` | `🐛` | `@debugger` `@debug` `@fix` | Nein |

---

## Was automatisch passiert

### Beim Start (`docker compose up -d`)

- Gateway-Container startet mit `openclaw:local` Image
- Volume-Mounts verbinden `~/.openclaw/` in den Container
- `host.docker.internal` wird auf den Host aufgeloest (Port 3000/8000 erreichbar)
- mcporter findet automatisch `/app/config/mcporter.json` (Symlink -> Volume-Mount)
- Context7 MCP-Server wird on-demand gestartet beim ersten Aufruf

### Wenn ein Agent eine Sandbox braucht

- OpenClaw liest `sandbox.docker` aus dem Agent-Eintrag in `openclaw.json`
- Erstellt automatisch einen Docker-Container mit dem angegebenen Image
- Wendet `binds`, `extraHosts`, `env`, `network` etc. an
- Fuehrt optionalen `setupCommand` nach Container-Erstellung aus

### mcporter im Gateway

- Der mcporter-Skill aktiviert sich automatisch, weil `which mcporter` das Binary findet
- Liest Config von `./config/mcporter.json` (= `/app/config/mcporter.json` im Container)
- Startet MCP-Server on-demand (Cold Start ~2-3s)

---

## Was manuell eingetragen/gepflegt werden muss

### A) Neuen Agent hinzufuegen

In `~/.openclaw/openclaw.json` -> `agents.list` Array:

```json
{
  "id": "mein-agent",
  "name": "Anzeigename",
  "workspace": "/Users/DEIN_USER/.openclaw/workspaces/mein-agent",
  "identity": {
    "name": "Kurzname",
    "avatar": "🤖"
  },
  "groupChat": {
    "mentionPatterns": ["@mein-agent"]
  }
}
```

Dann Workspace erstellen: `mkdir -p ~/.openclaw/workspaces/mein-agent`

### B) Sandbox fuer einen Agent aktivieren

Zum Agent-Eintrag `sandbox`-Block hinzufuegen:

```json
"sandbox": {
  "mode": "all",
  "scope": "agent",
  "workspaceAccess": "rw",
  "docker": {
    "image": "mein-image:tag",
    "network": "bridge",
    "extraHosts": ["host.docker.internal:host-gateway"],
    "binds": [
      "/host/pfad:/container/pfad:rw"
    ],
    "env": { "MY_VAR": "value" },
    "setupCommand": "npm install",
    "readOnlyRoot": false,
    "user": "0:0"
  }
}
```

### C) MCP-Server hinzufuegen

**Fuer den Gateway** (alle Agents ohne Sandbox):
`~/.openclaw/config/mcporter.json`

**Fuer die Tester-Sandbox** (Playwright etc.):
`~/.openclaw/config/sandbox-tester/mcporter.json`

Format:

```json
{
  "mcpServers": {
    "server-name": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@package/name"]
    }
  }
}
```

### D) Docker-Images bauen

Muss **manuell** ausgefuehrt werden wenn ein Dockerfile geaendert wurde:

```bash
# Gateway
docker build --build-arg INSTALL_DOCKER_CLI=1 -t openclaw:local .

# Tester-Sandbox (Playwright)
bash scripts/sandbox-tester-setup.sh

# Deploy-Sandbox
bash scripts/sandbox-deploy-setup.sh

# Danach neu starten
docker compose up -d
```

---

## Globale Defaults

In `agents.defaults` — gelten fuer **alle** Agents, sofern nicht ueberschrieben:

| Setting | Wert | Bedeutung |
|---------|------|-----------|
| `workspace` | `/home/node/.openclaw/workspace` | Fallback-Workspace im Container |
| `contextPruning.ttl` | `1h` | Kontext-Cache lebt 1 Stunde |
| `compaction.mode` | `safeguard` | Kompaktierung mit Sicherheitsnetz |
| `heartbeat.every` | `1h` | Agent meldet sich stuendlich |
| `maxConcurrent` | `4` | Max 4 Agents gleichzeitig aktiv |
| `subagents.maxConcurrent` | `8` | Max 8 Sub-Agents pro Agent |

---

## Netzwerk-Uebersicht

```
Host (macOS)
├── localhost:3000  (Frontend)
├── localhost:8000  (Backend)
│
├── Gateway Container (openclaw:local)
│   ├── Port 18789 -> Gateway API
│   ├── Port 18790 -> Bridge
│   ├── host.docker.internal -> Host
│   └── mcporter -> Context7
│
├── Tester Sandbox (openclaw-sandbox-tester:noble)
│   ├── host.docker.internal -> Host
│   ├── mcporter -> Context7 + Playwright
│   └── Chromium vorinstalliert
│
└── Deploy Sandbox (openclaw-sandbox-deploy:bookworm-slim)
    ├── host.docker.internal -> Host
    └── Docker Socket gemountet
```

**Wichtig:** In den Sandboxes immer `host.docker.internal:8000` statt `localhost:8000` verwenden.

---

## Dateien-Referenz

| Datei | Wo | Was |
|-------|----|-----|
| `~/.openclaw/openclaw.json` | Host | **Hauptconfig** — Agents, Sandbox, Gateway, Channels |
| `~/.openclaw/config/mcporter.json` | Host -> Gateway | MCP-Server fuer Gateway |
| `~/.openclaw/config/sandbox-tester/mcporter.json` | Host -> Tester-Sandbox | MCP-Server fuer Tester |
| `Dockerfile` | Repo | Gateway-Image (mcporter, Symlink, npm-Cache) |
| `Dockerfile.sandbox-tester` | Repo | Tester-Image (Playwright + mcporter) |
| `Dockerfile.sandbox-deploy` | Repo | Deploy-Image (Docker CLI) |
| `docker-compose.yml` | Repo | Gateway + CLI Container-Definition |

---

## Sandbox Docker Settings Referenz

Alle verfuegbaren Felder fuer `sandbox.docker`:

```typescript
{
  image?: string;                    // Docker Image
  containerPrefix?: string;          // Container-Name-Prefix
  workdir?: string;                  // Workdir Mount-Pfad (default: /workspace)
  readOnlyRoot?: boolean;            // Rootfs read-only
  tmpfs?: string[];                  // Extra tmpfs Mounts
  network?: string;                  // bridge | none | custom
  user?: string;                     // uid:gid
  capDrop?: string[];                // Linux Capabilities droppen
  env?: Record<string, string>;      // Umgebungsvariablen
  setupCommand?: string;             // Einmalig nach Erstellung
  pidsLimit?: number;                // PID-Limit
  memory?: string | number;          // RAM-Limit (z.B. "2g")
  memorySwap?: string | number;      // Swap-Limit
  cpus?: number;                     // CPU-Limit (z.B. 0.5)
  ulimits?: Record<string, ...>;     // ulimit Werte
  seccompProfile?: string;           // Seccomp-Profil
  apparmorProfile?: string;          // AppArmor-Profil
  dns?: string[];                    // DNS-Server
  extraHosts?: string[];             // Extra /etc/hosts Eintraege
  binds?: string[];                  // Volume-Mounts (host:container:mode)
}
```
