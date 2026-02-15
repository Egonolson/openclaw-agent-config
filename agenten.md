# Agenten-Konfiguration im Detail

Jeder Agent wird in `~/.openclaw/openclaw.json` unter `agents.list` definiert. Diese Datei beschreibt jeden Agent einzeln: Rolle, Konfiguration, Sandbox-Setup und Besonderheiten.

> Alle Pfade mit `/Users/YOUR_USER/` muessen durch deinen tatsaechlichen Home-Pfad ersetzt werden.

---

## Inhaltsverzeichnis

1. [Project Manager](#1-project-manager) — Koordination und Delegation
2. [Deploy & Docker](#2-deploy--docker) — Container-Management mit Docker-Socket
3. [Prototyper](#3-prototyper) — Schnelles Prototyping
4. [Production Engineer](#4-production-engineer) — Produktionsreife Implementierung
5. [Security Auditor](#5-security-auditor) — Sicherheitsanalysen
6. [Code Reviewer](#6-code-reviewer) — Code-Qualitaet und Reviews
7. [System Architect](#7-system-architect) — Architektur-Entscheidungen
8. [QA & Test Engineer](#8-qa--test-engineer) — Tests mit Playwright-Sandbox
9. [UI/UX Designer](#9-uiux-designer) — Interface-Design
10. [Debugger](#10-debugger) — Fehlersuche und Troubleshooting

---

## 1. Project Manager

**Rolle:** Zentrale Koordination. Delegiert Aufgaben an andere Agenten, trackt Fortschritt, fasst Ergebnisse zusammen.

| Eigenschaft | Wert |
|-------------|------|
| ID | `project-manager` |
| Default-Agent | **Ja** (erster Ansprechpartner) |
| Avatar | 📋 |
| Mentions | `@pm` `@projektmanager` `@project-manager` |
| Sandbox | Nein |
| Besonderheit | `allowAgents: ["*"]` — darf alle anderen Agents als Sub-Agents aufrufen |

### Konfiguration

```json
{
  "id": "project-manager",
  "default": true,
  "name": "Project Manager",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/project-manager",
  "identity": {
    "name": "PM",
    "avatar": "📋"
  },
  "groupChat": {
    "mentionPatterns": ["@pm", "@projektmanager", "@project-manager"]
  },
  "subagents": {
    "allowAgents": ["*"]
  }
}
```

### Was macht er automatisch?

- Erhaelt alle Nachrichten im Group-Chat standardmaessig (weil `default: true`)
- Kann jeden anderen Agent per `@mention` einbinden
- Hat Zugriff auf den geteilten Workspace unter `/shared-workspace`

### Was muss manuell konfiguriert werden?

- Nichts — funktioniert out-of-the-box
- Optional: `subagents.allowAgents` einschraenken auf bestimmte Agent-IDs

---

## 2. Deploy & Docker

**Rolle:** Baut und verwaltet Docker-Container, deployt Anwendungen, verwaltet Infrastruktur.

| Eigenschaft | Wert |
|-------------|------|
| ID | `deploy` |
| Avatar | 🐳 |
| Mentions | `@deploy` `@docker` |
| Sandbox | **Ja** — `openclaw-sandbox-deploy:bookworm-slim` |
| Besonderheit | Docker-Socket gemountet, Root-Zugriff im Container |

### Konfiguration

```json
{
  "id": "deploy",
  "name": "Deploy & Docker",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/deploy",
  "identity": {
    "name": "Deploy",
    "avatar": "🐳"
  },
  "groupChat": {
    "mentionPatterns": ["@deploy", "@docker"]
  },
  "sandbox": {
    "mode": "all",
    "scope": "agent",
    "workspaceAccess": "rw",
    "docker": {
      "image": "openclaw-sandbox-deploy:bookworm-slim",
      "network": "bridge",
      "extraHosts": ["host.docker.internal:host-gateway"],
      "binds": [
        "/Users/YOUR_USER/.docker/run/docker.sock:/var/run/docker.sock:rw",
        "/Users/YOUR_USER/.openclaw/workspaces:/workspaces:rw",
        "/Users/YOUR_USER/.openclaw/workspace:/shared-workspace:rw"
      ],
      "readOnlyRoot": false,
      "user": "0:0"
    }
  }
}
```

### Sandbox-Details

| Setting | Wert | Erklaerung |
|---------|------|------------|
| `mode: "all"` | Alle Befehle laufen in der Sandbox | Kein direkter Host-Zugriff |
| `scope: "agent"` | Eigener Container pro Agent-Session | Isoliert von anderen Agents |
| `user: "0:0"` | Root im Container | Noetig fuer Docker-Socket-Zugriff |
| `readOnlyRoot: false` | Beschreibbares Dateisystem | Fuer `apt-get install` etc. |
| `extraHosts` | `host.docker.internal` | Zugriff auf Host-Services (localhost:3000/8000) |

### Was muss manuell konfiguriert werden?

- **Docker-Image bauen:** `bash scripts/sandbox-deploy-setup.sh`
- **Docker-Socket-Pfad anpassen:** Auf Linux ist es `/var/run/docker.sock`, auf macOS Docker Desktop `/Users/YOUR_USER/.docker/run/docker.sock`
- **Host-Zugriff:** Im Container `host.docker.internal:PORT` statt `localhost:PORT` verwenden

---

## 3. Prototyper

**Rolle:** Schnelles Prototyping und Proof-of-Concepts. Erstellt funktionale Prototypen ohne Produktionsanspruch.

| Eigenschaft | Wert |
|-------------|------|
| ID | `prototyper` |
| Avatar | ⚡ |
| Mentions | `@prototyper` `@proto` `@prototype` |
| Sandbox | Nein |

### Konfiguration

```json
{
  "id": "prototyper",
  "name": "Prototyper",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/prototyper",
  "identity": {
    "name": "Prototyper",
    "avatar": "⚡"
  },
  "groupChat": {
    "mentionPatterns": ["@prototyper", "@proto", "@prototype"]
  }
}
```

### Was muss manuell konfiguriert werden?

- Nichts — laeuft ohne Sandbox direkt im Gateway
- Optional: Sandbox hinzufuegen wenn Isolation gewuenscht

---

## 4. Production Engineer

**Rolle:** Produktionsreife Implementierung. Schreibt robusten, getesteten Code mit Fehlerbehandlung.

| Eigenschaft | Wert |
|-------------|------|
| ID | `production` |
| Avatar | 🏭 |
| Mentions | `@production` `@prod` |
| Sandbox | Nein |

### Konfiguration

```json
{
  "id": "production",
  "name": "Production Engineer",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/production",
  "identity": {
    "name": "Production",
    "avatar": "🏭"
  },
  "groupChat": {
    "mentionPatterns": ["@production", "@prod"]
  }
}
```

### Was muss manuell konfiguriert werden?

- Nichts — laeuft ohne Sandbox direkt im Gateway

---

## 5. Security Auditor

**Rolle:** Sicherheitsanalysen, Vulnerability-Scans, OWASP-Checks, Threat Modeling.

| Eigenschaft | Wert |
|-------------|------|
| ID | `security-audit` |
| Avatar | 🔒 |
| Mentions | `@security` `@security-audit` `@sicherheit` |
| Sandbox | Nein |

### Konfiguration

```json
{
  "id": "security-audit",
  "name": "Security Auditor",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/security-audit",
  "identity": {
    "name": "Security",
    "avatar": "🔒"
  },
  "groupChat": {
    "mentionPatterns": ["@security", "@security-audit", "@sicherheit"]
  }
}
```

### Was muss manuell konfiguriert werden?

- Nichts — laeuft ohne Sandbox direkt im Gateway
- Optional: Sandbox mit Security-Tools (semgrep, trivy, etc.) hinzufuegen

---

## 6. Code Reviewer

**Rolle:** Code-Reviews, Best-Practice-Checks, Refactoring-Vorschlaege.

| Eigenschaft | Wert |
|-------------|------|
| ID | `code-audit` |
| Avatar | 🔍 |
| Mentions | `@code-audit` `@code-review` `@review` |
| Sandbox | Nein |

### Konfiguration

```json
{
  "id": "code-audit",
  "name": "Code Reviewer",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/code-audit",
  "identity": {
    "name": "Code Audit",
    "avatar": "🔍"
  },
  "groupChat": {
    "mentionPatterns": ["@code-audit", "@code-review", "@review"]
  }
}
```

### Was muss manuell konfiguriert werden?

- Nichts — laeuft ohne Sandbox direkt im Gateway

---

## 7. System Architect

**Rolle:** Architektur-Entscheidungen, System-Design, Technologie-Auswahl, API-Design.

| Eigenschaft | Wert |
|-------------|------|
| ID | `architect` |
| Avatar | 🏗️ |
| Mentions | `@architect` `@architektur` `@arch` |
| Sandbox | Nein |

### Konfiguration

```json
{
  "id": "architect",
  "name": "System Architect",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/architect",
  "identity": {
    "name": "Architect",
    "avatar": "🏗️"
  },
  "groupChat": {
    "mentionPatterns": ["@architect", "@architektur", "@arch"]
  }
}
```

### Was muss manuell konfiguriert werden?

- Nichts — laeuft ohne Sandbox direkt im Gateway

---

## 8. QA & Test Engineer

**Rolle:** Automatisierte Tests, E2E-Tests mit Playwright, Testabdeckung, Qualitaetssicherung.

| Eigenschaft | Wert |
|-------------|------|
| ID | `tester` |
| Avatar | 🧪 |
| Mentions | `@tester` `@test` `@qa` |
| Sandbox | **Ja** — `openclaw-sandbox-tester:noble` (Playwright) |
| Besonderheit | Chromium vorinstalliert, mcporter mit Playwright MCP |

### Konfiguration

```json
{
  "id": "tester",
  "name": "QA & Test Engineer",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/tester",
  "identity": {
    "name": "Tester",
    "avatar": "🧪"
  },
  "groupChat": {
    "mentionPatterns": ["@tester", "@test", "@qa"]
  },
  "sandbox": {
    "mode": "all",
    "scope": "agent",
    "workspaceAccess": "rw",
    "docker": {
      "image": "openclaw-sandbox-tester:noble",
      "network": "bridge",
      "extraHosts": ["host.docker.internal:host-gateway"],
      "binds": [
        "/Users/YOUR_USER/.openclaw/workspaces:/workspaces:rw",
        "/Users/YOUR_USER/.openclaw/workspace:/shared-workspace:rw",
        "/Users/YOUR_USER/.openclaw/config/sandbox-tester/mcporter.json:/home/pwuser/config/mcporter.json:ro"
      ],
      "readOnlyRoot": false,
      "user": "0:0"
    }
  }
}
```

### Sandbox-Details

| Setting | Wert | Erklaerung |
|---------|------|------------|
| `image` | `openclaw-sandbox-tester:noble` | Basiert auf `playwright:v1.50.0-noble` — Chromium vorinstalliert |
| `mode: "all"` | Alle Befehle in der Sandbox | Isolierte Testumgebung |
| `scope: "agent"` | Eigener Container | Testzustand bleibt isoliert |
| `extraHosts` | `host.docker.internal` | Tests koennen `host.docker.internal:3000` erreichen |
| mcporter-Bind | `mcporter.json:ro` | MCP-Config read-only gemountet |

### MCP-Server in der Sandbox

Die Datei `~/.openclaw/config/sandbox-tester/mcporter.json` wird per Bind-Mount in den Container gelegt:

```json
{
  "mcpServers": {
    "context7": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp@latest"]
    },
    "playwright": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-playwright"]
    }
  }
}
```

- **Context7**: Aktuelle Library-Dokumentation abrufen
- **Playwright MCP**: Browser-Automation (navigate, click, fill, screenshot) — funktioniert nur hier, weil Chromium vorinstalliert ist

### Was muss manuell konfiguriert werden?

- **Docker-Image bauen:** `bash scripts/sandbox-tester-setup.sh`
- **mcporter-Config erstellen:** `~/.openclaw/config/sandbox-tester/mcporter.json`
- **Host-Zugriff:** In Tests `host.docker.internal:PORT` statt `localhost:PORT` verwenden
- **Playwright-Tests:** URLs muessen auf `http://host.docker.internal:3000` zeigen

---

## 9. UI/UX Designer

**Rolle:** Interface-Design, Wireframes, Design-System, Barrierefreiheit.

| Eigenschaft | Wert |
|-------------|------|
| ID | `designer` |
| Avatar | 🎨 |
| Mentions | `@designer` `@design` `@ui` `@ux` |
| Sandbox | Nein |

### Konfiguration

```json
{
  "id": "designer",
  "name": "UI/UX Designer",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/designer",
  "identity": {
    "name": "Designer",
    "avatar": "🎨"
  },
  "groupChat": {
    "mentionPatterns": ["@designer", "@design", "@ui", "@ux"]
  }
}
```

### Was muss manuell konfiguriert werden?

- Nichts — laeuft ohne Sandbox direkt im Gateway

---

## 10. Debugger

**Rolle:** Fehlersuche, Root-Cause-Analyse, Log-Analyse, Performance-Debugging.

| Eigenschaft | Wert |
|-------------|------|
| ID | `debugger` |
| Avatar | 🐛 |
| Mentions | `@debugger` `@debug` `@fix` |
| Sandbox | Nein |

### Konfiguration

```json
{
  "id": "debugger",
  "name": "Debugger & Troubleshooter",
  "workspace": "/Users/YOUR_USER/.openclaw/workspaces/debugger",
  "identity": {
    "name": "Debugger",
    "avatar": "🐛"
  },
  "groupChat": {
    "mentionPatterns": ["@debugger", "@debug", "@fix"]
  }
}
```

### Was muss manuell konfiguriert werden?

- Nichts — laeuft ohne Sandbox direkt im Gateway
- Optional: Sandbox mit Debugging-Tools (strace, gdb, etc.) hinzufuegen

---

## Zusammenfassung: Was ist wo konfiguriert?

### Agents OHNE Sandbox (laufen im Gateway)

Diese Agents brauchen nur ihren Eintrag in `openclaw.json` und einen Workspace-Ordner:

| Agent | Workspace erstellen |
|-------|---------------------|
| Project Manager | `mkdir -p ~/.openclaw/workspaces/project-manager` |
| Prototyper | `mkdir -p ~/.openclaw/workspaces/prototyper` |
| Production | `mkdir -p ~/.openclaw/workspaces/production` |
| Security Auditor | `mkdir -p ~/.openclaw/workspaces/security-audit` |
| Code Reviewer | `mkdir -p ~/.openclaw/workspaces/code-audit` |
| Architect | `mkdir -p ~/.openclaw/workspaces/architect` |
| Designer | `mkdir -p ~/.openclaw/workspaces/designer` |
| Debugger | `mkdir -p ~/.openclaw/workspaces/debugger` |

### Agents MIT Sandbox (eigener Docker-Container)

Diese Agents brauchen zusaetzlich ein Docker-Image und ggf. Config-Dateien:

| Agent | Image | Image bauen | Extra-Config |
|-------|-------|-------------|--------------|
| Deploy | `openclaw-sandbox-deploy:bookworm-slim` | `bash scripts/sandbox-deploy-setup.sh` | Docker-Socket-Pfad anpassen |
| Tester | `openclaw-sandbox-tester:noble` | `bash scripts/sandbox-tester-setup.sh` | `config/sandbox-tester/mcporter.json` erstellen |

### Checkliste: Neuen Sandbox-Agent einrichten

1. Dockerfile erstellen (z.B. `Dockerfile.sandbox-meinagent`)
2. Build-Script erstellen (z.B. `scripts/sandbox-meinagent-setup.sh`)
3. Image bauen: `bash scripts/sandbox-meinagent-setup.sh`
4. Agent-Eintrag in `openclaw.json` mit `sandbox`-Block hinzufuegen
5. `extraHosts: ["host.docker.internal:host-gateway"]` nicht vergessen!
6. Workspace erstellen: `mkdir -p ~/.openclaw/workspaces/meinagent`
7. Optional: mcporter-Config per Bind-Mount einhaengen
8. Testen: Agent ansprechen und pruefen ob Sandbox-Container startet
