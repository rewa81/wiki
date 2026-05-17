# Claude Code - Komplettanleitung

**Claude Code** ist der **Coding-Agent von Anthropic**, der direkt in deiner IDE (VS Code, JetBrains) oder im Terminal läuft. Dieser Guide deckt Installation, Konfiguration und fortgeschrittene Nutzung ab.

---

## Installation

### Voraussetzungen

| System | Anforderungen |
|--------|--------------|
| **macOS** | macOS 11+ (Big Sur), Node.js 18+ |
| **Linux** | Ubuntu 20.04+, Debian 11+, Node.js 18+ |
| **Windows** | Windows 10/11, WSL2 (für Terminal-Version) |

### Installationsmethoden

#### Option 1: VS Code Extension (empfohlen)

1. **VS Code öffnen**
2. **Extension suchen:**
   - Öffne Extensions (Ctrl+Shift+X / Cmd+Shift+X)
   - Suche nach "Claude Code"
   - Installiere die offizielle Extension von Anthropic

3. **Authentifizierung:**
   - Klicke auf das Claude-Icon in der Sidebar
   - "Sign in with Anthropic" auswählen
   - API-Key eingeben oder Anthropic-Account verbinden

#### Option 2: CLI (Terminal)

```bash
# Node.js installieren (falls nicht vorhanden)
# macOS:
brew install node@18

# Linux (Ubuntu/Debian):
sudo apt update && sudo apt install -y nodejs npm

# Windows (Chocolatey):
choco install nodejs

# Claude Code CLI installieren
npm install -g @anthropics/claude-code

# Oder mit npx (keine globale Installation)
npx @anthropics/claude-code
```

#### Option 3: JetBrains Plugin

1. **Settings/Preferences** öffnen
2. **Plugins** → **Marketplace**
3. "Claude Code" suchen und installieren
4. IDE neustarten

### API-Key konfigurieren

```bash
# API-Key von Anthropic beziehen: https://console.anthropic.com/account/keys

# Für CLI:
claude-code --auth
# oder manuell:
export ANTHROPIC_API_KEY="dein-api-key"

# Für VS Code Extension:
# Automatique über UI oder in settings.json:
```json
{
  "claude-code.apiKey": "dein-api-key"
}
```
```

---

## Grundlegende Nutzung

### VS Code

| Tastenkürzel | Aktion |
|--------------|--------|
| `Ctrl+Shift+P` → "Claude: Ask" | Einmalige Frage stellen |
| `Ctrl+Shift+P` → "Claude: Chat" | Chat-Seite öffnen |
| `Ctrl+Shift+P` → "Claude: Edit" | Code bearbeiten lassen |
| `Ctrl+Shift+P` → "Claude: Command" | Claude-Befehl ausführen |

### CLI Befehle

| Befehl | Beschreibung |
|--------|--------------|
| `claude` | Interaktiver Modus starten |
| `claude --prompt "Frage"` | Einmalige Anfrage |
| `claude --workdir /pfad` | In bestimmtem Verzeichnis arbeiten |
| `claude --model <name>` | Bestimmtes Model verwenden |
| `claude --max-tokens N` | Maximale Tokens begrenzen |

### Interaktiver Modus

```bash
# Starten
claude

# Mit Arbeitsverzeichnis
claude --workdir ~/projekte/my-app

# Mit bestimmter Datei
claude --workdir ~/projekte/my-app --file src/main.py
```

### Non-Interactive Modus

```bash
# Einfache Frage
claude --prompt "Analysiere diesen Code und schlage Verbesserungen vor"

# Mit Datei-Kontext
claude --prompt "Was macht diese Funktion?" --file src/utils.py

# Mit Zeilenbereich
claude --prompt "Erkläre mir diese Funktion" --file src/main.py --line-range 10-25
```

---

## Konfiguration

### VS Code Settings

In `settings.json` (`Ctrl+,` → Open Settings (JSON)):

```json
{
  "claude-code.apiKey": "dein-api-key",
  "claude-code.model": "claude-3-5-sonnet-20250620",
  "claude-code.maxTokens": 4096,
  "claude-code.temperature": 0.7,
  "claude-code.showInlineSuggestions": true,
  "claude-code.completeSuggestions": true,
  "claude-code.codeLens": true,
  "claude-code.chat.sidebarWidth": 400,
  "claude-code.chat.fontSize": 14,
  "claude-code.allowedCommands": ["bash", "grep", "read_file"],
  "claude-code.blockedCommands": ["rm", "sudo", "chmod"]
}
```

### CLI Konfiguration

Datei: `~/.claude-code/config.json`

```json
{
  "apiKey": "dein-api-key",
  "model": "claude-3-5-sonnet-20250620",
  "maxTokens": 4096,
  "temperature": 0.7,
  "workdir": "~/projekte",
  "allowedCommands": ["bash", "grep", "read_file", "git"],
  "blockedCommands": ["rm -rf", "sudo", "chmod 777"],
  "sensitivePatterns": [".env", "*secret*", "*key*"],
  "skills": {
    "enabled": ["python-code-review", "git-best-practices"],
    "paths": ["~/.agent-skills/shared", "~/.claude/code/skills"]
  }
}
```

### Verfügbare Models

| Model | Beschreibung | Kontextfenster | Preis |
|-------|--------------|----------------|-------|
| `claude-3-5-sonnet-20250620` | Schnell & effizient | 200K | $$ |
| `claude-3-haiku-20240307` | Sehr schnell, günstig | 200K | $ |
| `claude-3-opus-20240229` | Stärkstes Model | 200K | $$$$ |
| `claude-2:1` | Älteres Model | 100K | $$ |

### Umgebungsvariablen

| Variable | Beschreibung |
|----------|--------------|
| `ANTHROPIC_API_KEY` | Anthropic API-Key |
| `ANTHROPIC_API_URL` | Custom API-Endpoint |
| `CLAUDE_CODE_MAX_TOKENS` | Maximale Tokens |
| `CLAUDE_CODE_MODEL` | Standard-Model |

---

## Skills verwalten

### Skills-Verzeichnisse

Claude Code durchsucht Skills in dieser Reihenfolge:

1. `<projekt>/.claude/skills/`
2. `<projekt>/.agents/skills/`
3. `~/.claude/code/skills/`
4. Konfigurierte Pfade in `config.json`

### Skill laden

```bash
# In Claude-Session
/skill load <skill-name>

# Beispiel
/skill load python-code-review
```

### Skill erstellen

1. **Verzeichnis erstellen:**
   ```bash
   mkdir -p ~/.claude/code/skills/meine-skill
   ```

2. **SKILL.md erstellen:**
   ```markdown
   ---
   name: meine-skill
   description: Meine erste Claude Code Skill
   ---
   
   # Meine Skill
   Diese Skill hilft bei...
   ```

3. **Skill testen:**
   ```bash
   /skill load meine-skill
   ```

### Skill deaktivieren

```bash
/skill unload <skill-name>
```

---

## Tools & Berechtigungen

### Verfügbare Tools

| Tool | Beschreibung | Standard-Berechtigung |
|------|--------------|----------------------|
| `bash` | Bash-Befehle ausführen | ask |
| `grep` | In Dateien suchen | always |
| `read_file` | Dateien lesen | always |
| `write_file` | Dateien schreiben | ask |
| `search_replace` | Text ersetzen | ask |
| `git` | Git-Operationen | ask |
| `fetch` | URLs abrufen | ask |

### Berechtigungen ändern

In `config.json`:

```json
{
  "allowedCommands": ["ls", "cd", "cat", "grep", "git"],
  "blockedCommands": ["rm", "sudo", "chmod", "mv"],
  "commandPermission": "ask"
}
```

### Sensible Muster

```json
{
  "sensitivePatterns": [
    ".env",
    "*.secret",
    "*.key",
    "*/.ssh/*",
    "*/.github/tokens/*",
    "config/"
  ]
}
```

---

## Projekt-Kontext

### Projekt-Dokumentation

Claude Code kann automatisch Projekt-Kontext laden:

1. **`.claudeignore`** – Dateien/Verzeichnisse ignorieren
   ```
   node_modules/
   .git/
   *.log
   .env
   ```

2. **`.claudeprompt`** – Projekt-spezifische Anweisungen
   ```
   # Projekt-Kontext für MyApp
   
   ## Übersicht
   - Sprache: Python 3.11
   - Framework: Django
   - Datenbank: PostgreSQL
   
   ## Wichtige Dateien
   - `src/` – Quellcode
   - `tests/` – Tests
   - `docs/` – Dokumentation
   
   ## Coding-Standards
   - PEP 8
   - Type Hints
   - Django Best Practices
   ```

### Code Lenses (Inline-Hilfen)

Aktiviere in VS Code:
```json
{
  "claude-code.codeLens": true
}
```

Fügt Clickable-Hints über Funktionen/Klassen ein:
- "Ask Claude to explain"
- "Ask Claude to improve"
- "Ask Claude to add tests"

### Inline-Completions

Aktiviere in VS Code:
```json
{
  "claude-code.completeSuggestions": true,
  "claude-code.showInlineSuggestions": true
}
```

Claude ergänzt automatisch Code während des Tippens.

---

## Integrierte IDE-Features

### VS Code

| Feature | Beschreibung |
|---------|--------------|
| **Chat View** | Dedizierter Chat-Bereich in der Sidebar |
| **Inline Chat** | Chat direkt im Editor |
| **Command Palette** | Alle Claude-Befehle über `Ctrl+Shift+P` |
| **Hover Info** | Erklärungen beim Hovern über Code |
| **Diagnostics** | Code-Probleme automatisch erkennen |

### JetBrains

| Feature | Beschreibung |
|---------|--------------|
| **Tool Window** | Claude-Chat als separates Tool-Fenster |
| **Code Completion** | Inline-Vervollständigungen |
| **Intentions** | Quick-Fix-Vorschläge |
| **Inspector** | Code-Analyse-Ergebnisse |

---

## Tipps & Tricks

### Nützliche Befehle

| Befehl | Beschreibung |
|--------|--------------|
| `/clear` | Chat-Verlauf löschen |
| `/help` | Hilfe anzeigen |
| `/model` | Model wechseln |
| `/max-tokens` | Maximale Tokens setzen |
| `/temperature` | Kreativität anpassen (0.0-1.0) |
| `/exit` | Session beenden |

### Häufige Probleme & Lösungen

| Problem | Lösung |
|---------|--------|
| "API key not found" | API-Key in Settings oder Umgebungsvariable setzen |
| "Model not available" | Andere Model-Version auswählen |
| "Command not allowed" | Berechtigungen in config.json prüfen |
| "Rate limit exceeded" | API-Key prüfen, Quota erhöhen |
| "Context window too small" | Model mit größerem Kontextfenster wählen |

### Kosten kontrollieren

```bash
# Maximale Tokens begrenzen
claude --prompt "..." --max-tokens 1000

# Kosten anzeigen (CLI)
claude --prompt "Was kostet das?"
```

---

## Updates

### Claude Code aktualisieren

**VS Code Extension:**
- Automatisch oder manuell über Extensions-View

**CLI:**
```bash
npm update -g @anthropics/claude-code
# oder
npm install -g @anthropics/claude-code@latest
```

### Changelog verfolgen

- [Claude Code Releases](https://github.com/anthropics/claude-code/releases)
- [Anthropic Blog](https://www.anthropic.com/blog)

---

## Deinstallation

**VS Code:**
1. Extensions-View öffnen
2. Claude Code finden
3. "Uninstall" klicken

**CLI:**
```bash
npm uninstall -g @anthropics/claude-code
rm -rf ~/.claude-code
```

**JetBrains:**
1. Settings → Plugins
2. Claude Code finden
3. "Uninstall" klicken
4. IDE neustarten

---

## Ressourcen

- [Offizielle Dokumentation](https://docs.anthropic.com/claude/code)
- [Anthropic Console](https://console.anthropic.com)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)
- [API Dokumentation](https://docs.anthropic.com/claude/reference)
- [Skills Directory](https://www.skills.sh)
