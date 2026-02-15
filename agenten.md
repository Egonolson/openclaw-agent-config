# Agenten-Konfiguration im Detail

Jeder Agent wird in `~/.openclaw/openclaw.json` unter `agents.list` definiert. Diese Datei beschreibt jeden Agent einzeln: Rolle, Konfiguration, Sandbox-Setup, **Tool-Profile** und **Skill-Allowlists**.

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
11. [Tool-Profile-Referenz](#tool-profile-referenz) — Die 4 verfuegbaren Profile
12. [Skill-Zuordnungs-Rationale](#skill-zuordnungs-rationale) — Warum welcher Agent welche Skills bekommt

---

## Uebersicht: Agent-Tabelle

| Agent | ID | Tool-Profil | Skills | Sandbox |
|-------|----|-------------|--------|---------|
| Project Manager | `project-manager` | `full` (Standard) | `github`, `trello`, `notion`, `slack`, `discord`, `session-logs`, `model-usage`, `clawhub` | Nein |
| Deploy & Docker | `deploy` | `full` (Standard) | `github`, `tmux`, `healthcheck`, `mcporter`, `session-logs` | Ja |
| Prototyper | `prototyper` | `full` (Standard) | `github`, `tmux`, `coding-agent`, `canvas`, `mcporter`, `session-logs` | Nein |
| Production Engineer | `production` | `full` (Standard) | `github`, `tmux`, `coding-agent`, `oracle`, `mcporter`, `session-logs` | Nein |
| Security Auditor | `security-audit` | `full` (Standard) | `github`, `healthcheck`, `oracle`, `session-logs` | Nein |
| Code Reviewer | `code-audit` | `full` (Standard) | `github`, `oracle`, `session-logs` | Nein |
| System Architect | `architect` | `full` (Standard) | `github`, `oracle`, `mcporter`, `session-logs` | Nein |
| QA & Test Engineer | `tester` | `full` (Standard) | `github`, `tmux`, `coding-agent`, `mcporter`, `session-logs` | Ja |
| UI/UX Designer | `designer` | `minimal` + alsoAllow | `nano-banana-pro`, `openai-image-gen`, `gifgrep`, `peekaboo`, `canvas`, `summarize`, `github` | Ja (rw) |
| Debugger | `debugger` | `full` (Standard) | `github`, `tmux`, `coding-agent`, `session-logs`, `mcporter` | Nein |

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
| Tool-Profil | `full` (Standard — kein explizites Profil gesetzt) |
| Skills | `github`, `trello`, `notion`, `slack`, `discord`, `session-logs`, `model-usage`, `clawhub` |
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
  },
  "skills": ["github", "trello", "notion", "slack", "discord", "session-logs", "model-usage", "clawhub"]
}
```

### Was macht er automatisch?

- Erhaelt alle Nachrichten im Group-Chat standardmaessig (weil `default: true`)
- Kann jeden anderen Agent per `@mention` einbinden
- Hat Zugriff auf den geteilten Workspace unter `/shared-workspace`

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/project-manager/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/project-manager/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/project-manager/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/project-manager/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/project-manager/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/project-manager/USER.md) | User-Kontext und Praeferenzen |

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
| Tool-Profil | `full` (Standard) |
| Skills | `github`, `tmux`, `healthcheck`, `mcporter`, `session-logs` |
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
  "skills": ["github", "tmux", "healthcheck", "mcporter", "session-logs"],
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

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/deploy/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/deploy/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/deploy/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/deploy/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/deploy/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/deploy/USER.md) | User-Kontext und Praeferenzen |

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
| Tool-Profil | `full` (Standard) |
| Skills | `github`, `tmux`, `coding-agent`, `canvas`, `mcporter`, `session-logs` |

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
  },
  "skills": ["github", "tmux", "coding-agent", "canvas", "mcporter", "session-logs"]
}
```

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/prototyper/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/prototyper/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/prototyper/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/prototyper/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/prototyper/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/prototyper/USER.md) | User-Kontext und Praeferenzen |

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
| Tool-Profil | `full` (Standard) |
| Skills | `github`, `tmux`, `coding-agent`, `oracle`, `mcporter`, `session-logs` |

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
  },
  "skills": ["github", "tmux", "coding-agent", "oracle", "mcporter", "session-logs"]
}
```

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/production/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/production/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/production/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/production/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/production/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/production/USER.md) | User-Kontext und Praeferenzen |

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
| Tool-Profil | `full` (Standard) |
| Skills | `github`, `healthcheck`, `oracle`, `session-logs` |

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
  },
  "skills": ["github", "healthcheck", "oracle", "session-logs"]
}
```

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/security-audit/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/security-audit/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/security-audit/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/security-audit/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/security-audit/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/security-audit/USER.md) | User-Kontext und Praeferenzen |

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
| Tool-Profil | `full` (Standard) |
| Skills | `github`, `oracle`, `session-logs` |

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
  },
  "skills": ["github", "oracle", "session-logs"]
}
```

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/code-audit/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/code-audit/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/code-audit/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/code-audit/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/code-audit/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/code-audit/USER.md) | User-Kontext und Praeferenzen |

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
| Tool-Profil | `full` (Standard) |
| Skills | `github`, `oracle`, `mcporter`, `session-logs` |

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
  },
  "skills": ["github", "oracle", "mcporter", "session-logs"]
}
```

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/architect/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/architect/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/architect/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/architect/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/architect/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/architect/USER.md) | User-Kontext und Praeferenzen |

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
| Tool-Profil | `full` (Standard) |
| Skills | `github`, `tmux`, `coding-agent`, `mcporter`, `session-logs` |
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
  "skills": ["github", "tmux", "coding-agent", "mcporter", "session-logs"],
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

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/tester/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/tester/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/tester/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/tester/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/tester/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/tester/USER.md) | User-Kontext und Praeferenzen |

### Was muss manuell konfiguriert werden?

- **Docker-Image bauen:** `bash scripts/sandbox-tester-setup.sh`
- **mcporter-Config erstellen:** `~/.openclaw/config/sandbox-tester/mcporter.json`
- **Host-Zugriff:** In Tests `host.docker.internal:PORT` statt `localhost:PORT` verwenden
- **Playwright-Tests:** URLs muessen auf `http://host.docker.internal:3000` zeigen

---

## 9. UI/UX Designer

**Rolle:** Interface-Design, Wireframes, Design-System, Barrierefreiheit, Bildgenerierung.

| Eigenschaft | Wert |
|-------------|------|
| ID | `designer` |
| Avatar | 🎨 |
| Mentions | `@designer` `@design` `@ui` `@ux` |
| Sandbox | `workspaceAccess: "rw"` |
| Tool-Profil | `minimal` + `alsoAllow` (fs, web, image, sessions) |
| Skills | `nano-banana-pro`, `openai-image-gen`, `gifgrep`, `peekaboo`, `canvas`, `summarize`, `github` |

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
  },
  "skills": ["nano-banana-pro", "openai-image-gen", "gifgrep", "peekaboo", "canvas", "summarize", "github"],
  "tools": {
    "profile": "minimal",
    "alsoAllow": ["group:fs", "group:web", "image", "group:sessions"]
  },
  "sandbox": {
    "workspaceAccess": "rw"
  }
}
```

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/designer/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/designer/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/designer/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/designer/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/designer/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/designer/USER.md) | User-Kontext und Praeferenzen |

### Was muss manuell konfiguriert werden?

- Workspace erstellen: `mkdir -p ~/.openclaw/workspaces/designer`

---

## 10. Debugger

**Rolle:** Fehlersuche, Root-Cause-Analyse, Log-Analyse, Performance-Debugging.

| Eigenschaft | Wert |
|-------------|------|
| ID | `debugger` |
| Avatar | 🐛 |
| Mentions | `@debugger` `@debug` `@fix` |
| Sandbox | Nein |
| Tool-Profil | `full` (Standard) |
| Skills | `github`, `tmux`, `coding-agent`, `session-logs`, `mcporter` |

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
  },
  "skills": ["github", "tmux", "coding-agent", "session-logs", "mcporter"]
}
```

### Workspace-Dateien

| Datei | Beschreibung |
|-------|-------------|
| [SOUL.md](agents/debugger/SOUL.md) | Kern-Identitaet und Rolle |
| [HEARTBEAT.md](agents/debugger/HEARTBEAT.md) | Automatische Health-Checks pro Heartbeat-Zyklus |
| [MEMORY.md](agents/debugger/MEMORY.md) | Dauerhafte Pflicht-Reviews und Arbeitskonventionen |
| [TOOLS.md](agents/debugger/TOOLS.md) | Lokale Tool-Konfigurationen |
| [IDENTITY.md](agents/debugger/IDENTITY.md) | Agent-Identitaet und Persoenlichkeit |
| [USER.md](agents/debugger/USER.md) | User-Kontext und Praeferenzen |

### Was muss manuell konfiguriert werden?

- Nichts — laeuft ohne Sandbox direkt im Gateway
- Optional: Sandbox mit Debugging-Tools (strace, gdb, etc.) hinzufuegen

---

## Tool-Profile-Referenz

OpenClaw definiert 4 Tool-Profile in `src/agents/tool-policy.ts`. Ein Profil bestimmt, welche nativen Tools ein Agent nutzen darf.

### Die 4 Profile

| Profil | Erlaubte Tools | Typischer Einsatz |
|--------|---------------|-------------------|
| **`minimal`** | `session_status` | Agents die fast nichts duerfen (Basis fuer Designer) |
| **`coding`** | `group:fs`, `group:runtime`, `group:sessions`, `group:memory`, `image` | Coding-fokussierte Agents |
| **`messaging`** | `message`, `sessions_list`, `sessions_history`, `sessions_send`, `session_status` | Reine Messaging-Agents |
| **`full`** | Alle Tools (kein `allow`/`deny`) | Standard wenn kein Profil gesetzt — alle Agents ohne explizites Profil |

### Tool-Gruppen

| Gruppe | Enthaltene Tools |
|--------|-----------------|
| `group:memory` | `memory_search`, `memory_get` |
| `group:web` | `web_search`, `web_fetch` |
| `group:fs` | `read`, `write`, `edit`, `apply_patch` |
| `group:runtime` | `exec`, `process` |
| `group:sessions` | `sessions_list`, `sessions_history`, `sessions_send`, `sessions_spawn`, `session_status` |
| `group:ui` | `browser`, `canvas` |
| `group:automation` | `cron`, `gateway` |
| `group:messaging` | `message` |
| `group:nodes` | `nodes` |

### Profil vs. Allow vs. AlsoAllow

```
tools.profile    → Basis-Allowlist (z.B. minimal = ["session_status"])
tools.allow      → ERSETZT die Profil-Allowlist komplett
tools.alsoAllow  → ERGAENZT die Profil-Allowlist additiv
tools.deny       → Blockiert Tools explizit (hat Vorrang)
```

**Empfehlung:** Nutze `profile` + `alsoAllow` statt eine komplett eigene `allow`-Liste. So profitiert man von Profil-Updates ohne alles manuell nachziehen zu muessen.

---

## Skill-Zuordnungs-Rationale

### Warum Skill-Allowlists?

Ohne `skills`-Feld in der Agent-Config bekommt jeder Agent **alle** installierten Skills (49+). Das fuehrt zu:

1. **Aufgeblaehte Kontexte:** Jeder Skill injiziert seine Beschreibung ins Kontextfenster — verschwendet Tokens
2. **Verwirrung:** Ein Designer-Agent sieht Skills wie `coding-agent`, `healthcheck`, `deploy` — und versucht sie zu nutzen
3. **Fehler:** EROFS-Fehler wenn ein Sandbox-Agent Tools nutzt die er nicht haben sollte

### Zuordnungsprinzipien

| Prinzip | Erklaerung |
|---------|------------|
| **Minimal noetig** | Jeder Agent bekommt nur Skills die zu seiner Rolle passen |
| **`github` fuer alle** | Alle Agents brauchen GitHub-Zugriff fuer Repo-Operationen |
| **`session-logs` fuer technische Agents** | Debugging und Monitoring brauchen Log-Zugriff |
| **`tmux` fuer ausfuehrende Agents** | Agents die Code ausfuehren brauchen Terminal-Multiplexing |
| **`coding-agent` selektiv** | Nur Agents die aktiv Code schreiben/debuggen |
| **`oracle` fuer analytische Agents** | Agents die tiefgreifende Codebase-Analyse brauchen |
| **`mcporter` fuer MCP-nutzende Agents** | Agents die MCP-Server konfigurieren/nutzen |

### Detailzuordnung

| Skill | Agents die ihn brauchen | Begruendung |
|-------|------------------------|-------------|
| `github` | Alle 10 | Repository-Zugriff ist universell noetig |
| `tmux` | Deploy, Prototyper, Production, Tester, Debugger | Terminal-Sessions fuer Code-Ausfuehrung |
| `coding-agent` | Prototyper, Production, Tester, Debugger | Aktives Code-Schreiben |
| `oracle` | Architect, Code Reviewer, Security, Production | Tiefgehende Code-Analyse |
| `mcporter` | Deploy, Prototyper, Production, Architect, Tester, Debugger | MCP-Server-Management |
| `session-logs` | Alle technischen Agents (nicht Designer) | Debugging und Session-Monitoring |
| `healthcheck` | Deploy, Security | Infrastruktur-Monitoring |
| `canvas` | Prototyper, Designer | UI-Prototyping |
| `slack`/`discord`/`trello`/`notion` | Nur PM | Projektmanagement-Kommunikation |
| `nano-banana-pro`/`openai-image-gen` | Nur Designer | Bildgenerierung |
| `gifgrep`/`peekaboo` | Nur Designer | Visuelle Assets |
| `summarize` | Nur Designer | Zusammenfassungen fuer Design-Briefs |
| `model-usage`/`clawhub` | Nur PM | Kosten-/Nutzungsmonitoring |

### Konfigurationsformat

```typescript
// In types.agents.ts
skills?: string[];  // omit = all skills; empty [] = none; array = allowlist
```

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
| Debugger | `mkdir -p ~/.openclaw/workspaces/debugger` |

### Agents MIT Sandbox (eigener Docker-Container)

Diese Agents brauchen zusaetzlich ein Docker-Image und ggf. Config-Dateien:

| Agent | Image | Image bauen | Extra-Config |
|-------|-------|-------------|--------------|
| Deploy | `openclaw-sandbox-deploy:bookworm-slim` | `bash scripts/sandbox-deploy-setup.sh` | Docker-Socket-Pfad anpassen |
| Tester | `openclaw-sandbox-tester:noble` | `bash scripts/sandbox-tester-setup.sh` | `config/sandbox-tester/mcporter.json` erstellen |
| Designer | Kein eigenes Image | — | `tools` + `sandbox.workspaceAccess` in `openclaw.json` |

### Checkliste: Neuen Sandbox-Agent einrichten

1. Dockerfile erstellen (z.B. `Dockerfile.sandbox-meinagent`)
2. Build-Script erstellen (z.B. `scripts/sandbox-meinagent-setup.sh`)
3. Image bauen: `bash scripts/sandbox-meinagent-setup.sh`
4. Agent-Eintrag in `openclaw.json` mit `sandbox`-Block hinzufuegen
5. `extraHosts: ["host.docker.internal:host-gateway"]` nicht vergessen!
6. Workspace erstellen: `mkdir -p ~/.openclaw/workspaces/meinagent`
7. Optional: mcporter-Config per Bind-Mount einhaengen
8. Testen: Agent ansprechen und pruefen ob Sandbox-Container startet
