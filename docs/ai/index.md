# KI-Agenten & Skill-Management

Hier findest du Anleitungen und Best Practices für den Einsatz von **KI-Coding-Agents** wie **Vibe CLI**, **Claude Code**, **Cursor** und anderen Tools. Im Fokus steht die **Wiederverwendbarkeit von Skills** (Fähigkeiten/Kontexten) über verschiedene Agenten hinweg.

## Übersicht

| Thema | Beschreibung |
|-------|--------------|
| [Skills Guide](./skills-guide.md) | Grundlagen: Was sind Skills, wie funktionieren sie, globale Verwaltung |
| [Skills Template](./skills-template.md) | Vorlage zum Erstellen eigener Skills (einfach kopieren & anpassen) |
| [Vibe Manual](./vibe-manual.md) | Komplettanleitung für Vibe CLI |
| [Claude Code Manual](./claude-code-manual.md) | Anleitung für Claude Code mit Skills |

---

## Warum Skills?

Skills (auch "Agents" oder "Workflows" genannt) ermöglichen es dir:

- **Wissen zu kapseln** – Spezifische Anleitungen, Best Practices oder Kontexte für bestimmte Aufgaben
- **Wiederverwendbar zu machen** – Eine Skill kann von mehreren Agenten genutzt werden
- **Projektübergreifend zu arbeiten** – Gleiche Skills in verschiedenen Projekten verwenden
- **Teamweit zu teilen** – Skills in einem zentralen Verzeichnis für alle Teammitglieder verfügbar machen

---

## Globales Skills-Verzeichnis

Empfohlene Struktur für ein **zentrales Skills-Verzeichnis**, das von allen Agenten genutzt werden kann:

```
~/.agent-skills/
├── README.md
├── vibe/
│   ├── my-vibe-skill/
│   │   └── SKILL.md
│   └── ...
├── claude/
│   ├── my-claude-skill/
│   │   └── SKILL.md
│   └── ...
└── shared/
    ├── git-best-practices/
    │   └── SKILL.md
    ├── python-linting/
    │   └── SKILL.md
    └── ...
```

**Vorteile:**
- Einmalige Pflege für alle Agenten
- Symlinks ermöglichen Nutzung in verschiedenen Projekten
- Versionierung über Git möglich

---

## Schnelleinstieg

1. **Globales Verzeichnis anlegen:**
   ```bash
   mkdir -p ~/.agent-skills/shared
   ```

2. **Erste Skill erstellen:**
   ```bash
   # Template kopieren
   cp ~/.agent-skills/shared/TEMPLATE/SKILL.md ~/.agent-skills/shared/meine-skill/SKILL.md
   ```

3. **Mit Agenten verknüpfen:**
   ```bash
   # Für Vibe
   ln -s ~/.agent-skills/shared/meine-skill ~/.vibe/skills/meine-skill
   
   # Für Claude Code
   ln -s ~/.agent-skills/shared/meine-skill ~/.claude/code/skills/meine-skill
   ```

4. **Skill laden:**
   - **Vibe:** `/skill lade meine-skill`
   - **Claude Code:** (automatisch, wenn im Skills-Pfad)

---

## Tools & Ressourcen

| Tool | Beschreibung | Link |
|------|--------------|------|
| **skills.sh** | Offizielles Skills-Verzeichnis für KI-Agenten | [skills.sh](https://www.skills.sh) |
| **Vibe CLI** | Mistral AI's Coding Agent | [GitHub](https://github.com/mistralai/vibe) |
| **Claude Code** | Anthropic's Coding Agent | [GitHub](https://github.com/anthropics/claude-code) |
