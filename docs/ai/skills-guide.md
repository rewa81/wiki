# Skills Guide: Globale Verwaltung & Symlinks

## Was sind Skills?

Skills (oder "Agent Skills") sind **Kontext-Pakete** für KI-Coding-Agents. Sie enthalten:

- **Anweisungen** – Wie der Agent bestimmte Aufgaben lösen soll
- **Tools** – Welche Werkzeuge die Skill nutzen darf
- **Kontext** – Hintergrundwissen, Best Practices, Code-Beispiele
- **Workflows** – Schritt-für-Schritt-Anleitungen für komplexe Aufgaben

Jede Skill ist eine **Markdown-Datei** (meist `SKILL.md`) mit speziellem YAML-Frontmatter.

---

## Globales Skills-Verzeichnis einrichten

### Warum global?

| Problem | Lösung mit globalem Verzeichnis |
|---------|-------------------------------|
| Skills in jedem Projekt dupliziert | Einmalige Pflege, überall nutzbar |
| Unterschiedliche Versionen | Zentrale Versionierung über Git |
| Teamweite Nutzung schwierig | Ein gemeinsames Verzeichnis für alle |
| Agent-spezifische Anpassungen | Unterverzeichnisse pro Agent |

### Empfohlene Struktur

```
~/.agent-skills/
├── README.md                  # Dokumentation des Verzeichnisses
├── .gitignore                 # Ignoriert lokale Anpassungen
├── shared/                    # Skills für alle Agenten
│   ├── python/
│   │   ├── code-review/
│   │   │   └── SKILL.md
│   │   ├── testing/
│   │   │   └── SKILL.md
│   │   └── linting/
│   │       └── SKILL.md
│   ├── git/
│   │   ├── best-practices/
│   │   │   └── SKILL.md
│   │   └── commit-messages/
│   │       └── SKILL.md
│   └── docker/
│       └── best-practices/
│           └── SKILL.md
├── vibe/                      # Vibe-spezifische Skills
│   └── my-custom-skill/
│       └── SKILL.md
├── claude/                    # Claude Code Skills
│   └── my-claude-skill/
│       └── SKILL.md
└── cursor/                    # Cursor Skills
    └── my-cursor-skill/
        └── SKILL.md
```

### Verzeichnis anlegen

```bash
# Hauptverzeichnis
mkdir -p ~/.agent-skills/{shared,vibe,claude,cursor}

# Initiales Git-Repository
cd ~/.agent-skills
git init
echo "# Agent Skills" > README.md
echo "*.pyc" > .gitignore
echo "__pycache__/" >> .gitignore
echo ".DS_Store" >> .gitignore
git add README.md .gitignore
git commit -m "Initial commit: Agent Skills Verzeichnis"
```

---

## Symlinks: Skills in Projekten nutzen

### Grundprinzip

```
Projekt-Verzeichnis/
├── .vibe/
│   └── skills/          # --> Symlink zu ~/.agent-skills/shared
│       ├── python/     # --> Symlink zu ~/.agent-skills/shared/python
│       └── git/        # --> Symlink zu ~/.agent-skills/shared/git
└── src/
```

### Symlinks erstellen

#### Option 1: Einzelne Skills verlinken

```bash
# Von globalem Verzeichnis in Projekt
ln -s ~/.agent-skills/shared/git/best-practices ~/.vibe/skills/git-best-practices

# Für Claude Code
ln -s ~/.agent-skills/shared/git/best-practices ~/.claude/code/skills/git-best-practices
```

#### Option 2: entire shared-Verzeichnis verlinken

```bash
# Komplettes shared-Verzeichnis verlinken
ln -s ~/.agent-skills/shared ~/.vibe/skills/shared
ln -s ~/.agent-skills/shared ~/.claude/code/skills/shared
```

#### Option 3: Alle Skills auf einmal (Bash-Skript)

```bash
#!/bin/bash
# link-skills.sh

SKILLS_DIR="$HOME/.agent-skills/shared"
TARGET_DIR="$HOME/.vibe/skills"

# Verzeichnis erstellen
mkdir -p "$TARGET_DIR"

# Alle Skills verlinken
for skill in "$SKILLS_DIR"/*/; do
    skill_name=$(basename "$skill")
    ln -sf "$skill" "$TARGET_DIR/$skill_name"
    echo "Linked: $skill_name"
done

# Auch für Claude Code
TARGET_CLAUDE="$HOME/.claude/code/skills"
mkdir -p "$TARGET_CLAUDE"
for skill in "$SKILLS_DIR"/*/; do
    skill_name=$(basename "$skill")
    ln -sf "$skill" "$TARGET_CLAUDE/$skill_name"
done
```

### Symlinks verwalten

| Befehl | Beschreibung |
|--------|--------------|
| `ln -s <quelle> <ziel>` | Symlink erstellen |
| `ln -sf <quelle> <ziel>` | Symlink ersetzen (force) |
| `ls -la <pfad>` | Symlinks anzeigen (zeigt mit `->` auf Ziel) |
| `readlink <symlink>` | Ziel eines Symlinks anzeigen |
| `rm <symlink>` | Symlink löschen (nicht das Ziel!) |
| `find . -type l` | Alle Symlinks im aktuellen Verzeichnis finden |

---

## skills.sh: Offizielles Skills-Verzeichnis

[skills.sh](https://www.skills.sh) ist eine **Community-Plattform** für KI-Agent-Skills.

### Skills von skills.sh installieren

#### Über npx (empfohlen)

```bash
# Skill suchen
npx skills search "python"

# Skill installieren
npx skills add vercel-labs/skills@python-best-practices

# Installierte Skills auflisten
npx skills list

# Skill aktualisieren
npx skills update vercel-labs/skills@python-best-practices
```

#### Manuell von GitHub

```bash
# Repository klonen
git clone https://github.com/vercel-labs/skills.git ~/.agent-skills/external/vercel-skills

# Spezifische Skill verlinken
ln -s ~/.agent-skills/external/vercel-skills/skills/python-best-practices ~/.vibe/skills/python-best-practices
```

### Beliebte Skills von skills.sh

| Skill | Beschreibung | Repository |
|-------|--------------|------------|
| python-best-practices | Python-Entwicklung Best Practices | vercel-labs/skills |
| git-workflow | Git-Workflow-Standards | vercel-labs/skills |
| security-audit | Sicherheitsaudits | vercel-labs/skills |
| docker-best-practices | Docker Best Practices | vercel-labs/skills |
| typescript-setup | TypeScript-Projekt-Setup | vercel-labs/skills |

---

## Skill-Aufbau: YAML-Frontmatter

Jede Skill beginnt mit einem **YAML-Frontmatter**, das Metadaten enthält:

```yaml
---
name: python-code-review          # Eindeutiger Name (a-z, 0-9, ",-")
description: |                   # Beschreibung (mehrzeilig möglich)
  Führt Code-Reviews für Python-Projekte durch.
  Prüft auf PEP 8 Compliance, Typisierung und Sicherheitslücken.

user-invocable: true             # Kann der Nutzer die Skill manuell laden?

# Welche Tools diese Skill verwenden darf
allowed-tools:
  - bash
  - grep
  - read_file
  - search_replace

# Welche Tools NICHT verwendet werden dürfen
disallowed-tools:
  - write_file

# Muster/Dateien, die ignoriert werden sollen
ignore-patterns:
  - ".env"
  - "*/tests/*"
  - "*/node_modules/*"

# Abhängigkeiten von anderen Skills
dependencies:
  - python-linting
  - git-best-practices
---

# Skill-Inhalt (Markdown)
## Python Code Review Skill

Diese Skill analysiert Python-Code auf:
- PEP 8 Compliance
- Typisierung (mypy)
- Sicherheitslücken (bandit)
- Code-Duplikate

### Verwendung

1. Skill laden: `/skill lade python-code-review`
2. Datei oder Verzeichnis angeben: `Analysiere src/module.py`

### Beispiele

...