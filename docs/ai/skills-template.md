# Skill Template

**Kopiere diese Vorlage**, um schnell eine neue Skill zu erstellen. Speichere sie als `SKILL.md` in einem eigenen Verzeichnis (z.B. `~/.agent-skills/shared/meine-skill/SKILL.md`).

---

## Grundgerüst

```markdown
---
name: DEIN-SKILL-NAME          # z.B. "python-code-review", nur Kleinbuchstaben, Bindestriche, keine Leerzeichen
description: |                 # Kurze Beschreibung (1-2 Sätze)
  [Kurze Beschreibung, was diese Skill macht und wann sie verwendet werden soll.]

user-invocable: true           # true = Nutzer kann Skill manuell mit /skill lade <name> aufrufen

# Tools
allowed-tools:                # Welche Tools die Skill verwenden darf (leer = alle)
  - bash
  - grep
  - read_file
  - write_file
  - search_replace

disallowed-tools:             # Welche Tools explizit NICHT verwendet werden dürfen
  - task                      # Sub-Agents nicht erlaubt

# Sicherheits-Einstellungen
sensitive-patterns:          # Dateien, die NIE gelesen/änderbar sind
  - ".env"
  - "*secret*"
  - "*/.ssh/*"
  - "*/.github/tokens/*"

# Abhängigkeiten
dependencies:                # Andere Skills, die geladen sein müssen
  - git-best-practices
  - python-linting

# Metadaten
author: Dein Name
version: 1.0.0
tags:                        # Kategorien für die Suche
  - python
  - code-review
  - quality
---

# [Skill-Name]

[Ausführliche Beschreibung der Skill und ihrer Funktionalität]

## Wann diese Skill verwenden

- [ ] [Anwendungsfall 1]
- [ ] [Anwendungsfall 2]
- [ ] [Anwendungsfall 3]

## Voraussetzungen

- [ ] Python 3.11+
- [ ] Node.js 18+
- [ ] Docker
- [ ] Git

## Verwendung

### Grundlegende Nutzung

```
Nutzer: /skill lade [skill-name]
Agent:  [Skill geladen. Wie kann ich dir helfen?]
Nutzer: [Deine Anfrage]
```

### Beispiele

| Anfrage | Aktion |
|---------|--------|
| `"Analysiere dieses Python-Modul auf Sicherheitslücken"` | Führt bandit, safety, semgrep aus |
| `"Optimiere diesen Code für Performance"` | Analysiert und schlägt Optimierungen vor |
| `"Erstelle einen Unit-Test für diese Funktion"` | Generiert pytest-Tests |

## Konfiguration

### Umgebungsvariablen

| Variable | Beschreibung | Standardwert |
|----------|--------------|---------------|
| `MAX_LINE_LENGTH` | Maximale Zeilenlänge | 88 |
| `PYTHON_VERSION` | Python-Version | 3.11 |

### Einstellungen

```yaml
# Optional: Skill-spezifische Konfiguration in .vibe/config.toml
[skills.""]
max_tokens = 10000
allow_operations = ["read", "write"]
```

## Implementierungs-Details

### Schritt-für-Schritt Workflow

1. **Analysephase**
   - Dateien lesen
   - Abhängigkeiten prüfen
   - Konfiguration validieren

2. **Ausführungsphase**
   - [ ] Schritt 1
   - [ ] Schritt 2
   - [ ] Schritt 3

3. **Validierungsphase**
   - Ergebnisse prüfen
   - Änderungen testen
   - Report erstellen

### Fehlerbehandlung

| Fehler | Lösung |
|--------|--------|
| Datei nicht gefunden | Nutzer auffordern, Pfad anzugeben |
| Keine Schreibrechte | Nur Lesemodus aktivieren |
| Tool nicht verfügbar | Nutzer über fehlende Tools informieren |

## Best Practices

### Do's
- [ ] Klare, spezifische Anweisungen geben
- [ ] Immer den Kontext erklären
- [ ] Sicherheitshinweise einbauen
- [ ] Beispiele und Code-Snippets einfügen

### Don'ts
- [ ] Keine unsicheren Operationen (rm -rf, chmod 777)
- [ ] Keine persönlichen Daten anzeigen
- [ ] Keine externen APIs ohne Nutzerbestätigung aufrufen
- [ ] Keine endlosen Schleifen

## Tests

### Testfälle

```markdown
### Test 1: Grundlegende Funktionalität
- Eingabe: `"Analysiere diese Datei"`
- Erwartet: Datei wird gelesen und analysiert
- Status: ✅

### Test 2: Fehlerfall
- Eingabe: `"Lösche alle Dateien"`
- Erwartet: Ablehnung mit Sicherheitshinweis
- Status: ✅
```

## Changelog

| Version | Datum | Änderungen |
|---------|-------|------------|
| 1.0.0 | YYYY-MM-DD | Erste Version |

## Lizenz

[MIT License](https://opensource.org/licenses/MIT) oder spezifizieren:

```
Copyright (c) <span id="template-year">2024</span> Dein Name

Permission is hereby granted...
```

<script>document.getElementById('template-year').textContent = new Date().getFullYear();</script>

---

## Beispiel: Komplette Python-Code-Review Skill

```markdown
---
name: python-code-review
description: |
  Führt umfassende Code-Reviews für Python-Projekte durch.
  Prüft auf PEP 8, Typisierung, Sicherheitslücken und Code-Qualität.

user-invocable: true

allowed-tools:
  - bash
  - grep
  - read_file
  - python

sensitive-patterns:
  - ".env"
  - "*/tests/fixtures/*"

tags:
  - python
  - code-review
  - quality
  - security
---

# Python Code Review Skill

Diese Skill analysiert Python-Code auf:
- PEP 8 Compliance (flake8)
- Typisierung (mypy)
- Sicherheitslücken (bandit, safety)
- Code-Qualität (pylint)
- Import-Sortierung (isort)

## Verwendung

1. Skill laden: `/skill lade python-code-review`
2. Datei oder Verzeichnis angeben

### Beispiele

- `"Analysiere src/module.py"`
- `"Prüfe das gesamte Projekt auf Sicherheitslücken"`
- `"Zeige mir die 10 schlimmsten Code-Probleme"`

## Workflow

1. **Statische Analyse**
   - flake8 für PEP 8
   - mypy für Typisierung
   - bandit für Sicherheitslücken

2. **Dynamische Analyse**
   - Import-Checks
   - Zirkuläre Abhängigkeiten

3. **Report**
   - Zusammenfassung der Probleme
   - Priorisierte Liste
   - Lösungsvorschläge
```
