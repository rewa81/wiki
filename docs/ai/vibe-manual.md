# Vibe CLI - Komplettanleitung

**Vibe** ist der **Coding-Agent von Mistral AI**, der dir bei der Softwareentwicklung hilft. Dieser Guide deckt alles von der Installation bis zu fortgeschrittenen Skills ab.

---

## Installation

### Voraussetzungen

| System | Anforderungen |
|--------|--------------|
| **macOS** | Python 3.11+, Git |
| **Linux** | Python 3.11+, Git, gcc |
| **Windows** | Python 3.11+, Git, WSL2 (empfohlen) |

### Installationsschritte

#### 1. Python installieren

**macOS (Homebrew):**
```bash
brew install python@3.11
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install python3.11 python3.11-venv python3-pip git
```

**Windows (Chocolatey):**
```powershell
choco install python git
```

#### 2. Vibe installieren

```bash
# Virtuelle Umgebung erstellen (empfohlen)
python3.11 -m venv ~/.venv/vibe
source ~/.venv/vibe/bin/activate  # Linux/macOS
# .\.venv\vibe\Scripts\activate  # Windows

# Vibe installieren
pip install mistral-vibe
```

#### 3. API-Key konfigurieren

```bash
# Interaktiver Setup
vibe --setup

# Oder manuell
mkdir -p ~/.vibe
echo '{"api_key": "dein-mistral-api-key"}' > ~/.vibe/.env
```

**API-Key beziehen:** [Mistral AI Console](https://console.mistral.ai/api-keys/)

#### 4. PATH hinzufügen (für globales vibe-Kommando)

```bash
# Zu ~/.zshrc oder ~/.bashrc hinzufügen
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

#### 5. Testen

```bash
vibe --version
vibe "Hallo, bist du bereit?"
```

---

## Grundlegende Nutzung

### Befehle Übersicht

| Befehl | Beschreibung |
|--------|--------------|
| `vibe` | Interaktiver Modus starten |
| `vibe -p "Prompt"` | Einmaliger Prompt, auto-approve |
| `vibe --workdir /pfad` | In bestimmtem Verzeichnis arbeiten |
| `vibe --agent <name>` | Bestimmten Agent verwenden |
| `vibe --enabled-tools <tools>` | Nur bestimmte Tools aktivieren |
| `vibe --max-turns N` | Maximale Antworten begrenzen |
| `vibe --max-price DOLLARS` | Maximale Kosten begrenzen |

### Interaktiver Modus

```bash
# Starten
vibe

# Mit Arbeitsverzeichnis
vibe --workdir ~/projekte/my-app

# Mit bestimmten Tools
vibe --enabled-tools bash read_file grep
```

### Programmatischer Modus (non-interactive)

```bash
# Einfache Anfrage
vibe -p "Analysiere diese Datei und sag mir, was sie macht"

# Mit Kostenlimit
vibe -p "Erstelle eine API" --max-price 5

# Nur bestimmte Tools erlauben
vibe -p "Suche nach TODO" --enabled-tools grep
```

---

## Konfiguration

### config.toml

Vibe-Konfiguration liegt in `~/.vibe/config.toml`:

```toml
# Standardmodell
active_model = "mistral-medium-3.5"

# API-Provider
[[providers]]
name = "mistral"
api_base = "https://api.mistral.ai/v1"
api_key_env_var = "MISTRAL_API_KEY"

# Tools
[tools.bash]
permission = "ask"  # "always", "ask", "never"
allowlist = ["ls", "cd", "cat"]
denylist = ["rm", "sudo"]

# Skills
skill_paths = ["~/.agent-skills", "~/.vibe/skills"]
enabled_skills = []
disabled_skills = []

# Agenten
default_agent = "default"
```

### Wichtige Einstellungen

| Einstellung | Beschreibung | Werte |
|-------------|--------------|-------|
| `active_model` | Standardmodell | `mistral-medium-3.5`, `mistral-large-2407`, etc. |
| `vim_keybindings` | Vim-Tastenkürzel | `true`/`false` |
| `autocopy_to_clipboard` | Antworten automatisch kopieren | `true`/`false` |
| `displayed_workdir` | Arbeitsverzeichnis anzeigen | Pfad |
| `enable_telemetry` | Anonymisierte Daten senden | `true`/`false` |

### Umgebungsvariablen

| Variable | Beschreibung |
|----------|--------------|
| `MISTRAL_API_KEY` | Mistral API-Key |
| `VIBE_MAX_TURNS` | Maximale Antworten |
| `VIBE_MAX_PRICE` | Maximale Kosten in $ |
| `VIBE_HOME` | Vibe-Konfigurationsverzeichnis |

---

## Skills verwalten

### Skills-Verzeichnisse

Vibe durchsucht Skills in dieser Reihenfolge:

1. `skill_paths` aus `config.toml`
2. `<projekt>/.vibe/skills/`
3. `<projekt>/.agents/skills/`
4. `~/.vibe/skills/`

### Skill laden

```bash
# In Vibe-Session
/skill lade <skill-name>

# Beispiel
/skill lade python-code-review
```

### Skill erstellen

1. **Verzeichnis erstellen:**
   ```bash
   mkdir -p ~/.vibe/skills/meine-skill
   ```

2. **SKILL.md erstellen:**
   ```markdown
   ---
   name: meine-skill
   description: Meine erste Custom Skill
   user-invocable: true
   allowed-tools:
     - bash
     - read_file
   ---
   
   # Meine Skill
   Diese Skill hilft bei...
   ```

3. **Skill testen:**
   ```bash
   /skill lade meine-skill
   ```

### Skill deaktivieren

```bash
/skill entladen <skill-name>
```

---

## Tools & Berechtigungen

### Verfügbare Tools

| Tool | Beschreibung | Standard-Berechtigung |
|------|--------------|----------------------|
| `bash` | Bash-Befehle ausführen | `ask` |
| `grep` | In Dateien suchen | `always` |
| `read_file` | Dateien lesen | `always` |
| `write_file` | Dateien schreiben | `ask` |
| `search_replace` | Text ersetzen | `ask` |
| `todo` | Aufgabenliste verwalten | `always` |
| `task` | Sub-Agents erstellen | `ask` |
| `git` | Git-Operationen | `ask` |
| `web_search` | Web-Suche | `ask` |
| `web_fetch` | Webseiten abrufen | `ask` |

### Berechtigungen ändern

In `~/.vibe/config.toml`:

```toml
[tools.bash]
permission = "always"  # oder "ask", "never"
allowlist = ["ls", "cd", "cat", "grep"]
denylist = ["rm", "sudo", "chmod"]

[tools.write_file]
permission = "ask"
max_write_bytes = 64000
```

### Sensible Muster

Bestimmte Dateien werden automatisch ignoriert:

```toml
[tools.bash]
sensitive_patterns = ["sudo", "rm -rf", "chmod 777"]

[tools.read_file]
sensitive_patterns = [".env", "*.secret", "*key*"]
```

---

## Agenten

Vibe unterstützt verschiedene **Agenten** (Verhaltensmuster):

| Agent | Beschreibung |
|-------|--------------|
| `default` | Standard-Agent |
| `plan` | Erstellt zuerst einen Plan |
| `accept-edits` | Akzeptiert alle Dateiänderungen automatisch |
| `auto-approve` | Approved alle Tools automatisch |

### Agent erstellen

1. **Datei erstellen:** `~/.vibe/agents/mein-agent.toml`

   ```toml
   name = "Mein Agent"
   description = "Ein spezialisierter Agent für..."
   model = "mistral-medium-3.5"
   
   [behavior]
   max_turns = 10
   auto_approve_tools = true
   
   [tools]
   enabled = ["bash", "read_file", "grep"]
   disabled = ["write_file"]
   ```

2. **Agent verwenden:**
   ```bash
   vibe --agent mein-agent
   ```

---

## Projekt-Kontext

Vibe kann **Projekt-spezifischen Kontext** automatisch laden:

### Projekt-Dokumentation

Erstelle eine `.vibe.md` oder `.vibeprompt` Datei im Projekt:

```markdown
# Projekt-Kontext für MyApp

## Übersicht
- **Sprache:** Python 3.11
- **Framework:** FastAPI
- **Datenbank:** PostgreSQL

## Wichtige Dateien
- `src/main.py` - Haupteinstiegspunkt
- `src/models/` - Datenbankmodelle
- `src/routes/` - API-Routen

## Best Practices
- Verwende immer Typisierung
- Schreibe Unit-Tests
- Nutze Pydantic für Validierung
```

### Git-Kontext

Vibe kann automatisch Git-Informationen laden:

```toml
[project_context]
default_commit_count = 5  # Letzte 5 Commits anzeigen
timeout_seconds = 2.0
```

---

## Tipps & Tricks

### Nützliche Aliase

Zu `~/.zshrc` oder `~/.bashrc` hinzufügen:

```bash
# Vibe mit Arbeitsverzeichnis
alias vibew="vibe --workdir ."

# Vibe mit bestimmten Tools
alias viberead="vibe --enabled-tools read_file grep"

# Vibe im Projekt-Verzeichnis
alias vibep="cd ~/projekte/current && vibe"
```

### Häufige Probleme & Lösungen

| Problem | Lösung |
|---------|--------|
| `command not found: vibe` | `export PATH="$HOME/.local/bin:$PATH"` |
| API-Key nicht gefunden | `vibe --setup` oder `MISTRAL_API_KEY` setzen |
| Tools werden blockiert | Berechtigungen in `config.toml` prüfen |
| Skill wird nicht gefunden | `skill_paths` in `config.toml` prüfen |
| Hohe Kosten | `--max-price` oder `--max-turns` verwenden |

### Kosten kontrollieren

```bash
# Maximale Kosten pro Session
vibe -p "..." --max-price 10

# Maximale Antworten
vibe -p "..." --max-turns 5

# Kosten anzeigen
vibe -p "Was kostet das?" --output json | jq '.cost'
```

---

## Updates

### Vibe aktualisieren

```bash
pip install --upgrade mistral-vibe
```

### Models wechseln

Verfügbare Models:
- `mistral-medium-3.5` (Standard)
- `mistral-large-2407` (stärker, teurer)
- `mistral-large-2402` (älter, günstiger)

```bash
vibe -p "Test" --model mistral-large-2407
```

---

## Deinstallation

```bash
# Virtuelle Umgebung löschen
rm -rf ~/.venv/vibe

# Vibe-Konfiguration löschen
rm -rf ~/.vibe

# Paket deinstallieren
pip uninstall mistral-vibe
```

---

## Ressourcen

- [Offizielle Dokumentation](https://github.com/mistralai/vibe)
- [Mistral API Console](https://console.mistral.ai)
- [Vibe GitHub Repository](https://github.com/mistralai/vibe)
- [Skills Directory](https://www.skills.sh)
