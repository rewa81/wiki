# Python Setup mit uv auf macOS

## Was ist uv?

[uv](https://docs.astral.sh/uv/) ist ein extrem schneller Python Package Manager und Project Manager, geschrieben in Rust. Er ersetzt `pip`, `venv`, `virtualenv` und weitere Tools durch eine einzige, performante Lösung.

**Vorteile:**

- ⚡ 10-100x schneller als pip
- 🔒 Automatisches Dependency Management mit Lock-Files
- 🎯 Integriertes Virtual Environment Management
- 🚀 Einfache Installation und Verwendung

## Installation von uv

### Mit Homebrew (empfohlen)

```sh
brew install uv
```

### Mit Shell-Script

```sh
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Mit pip

```sh
pip install uv
```

Nach der Installation solltest du das Terminal neu starten oder den Pfad aktualisieren:

```sh
source ~/.zshrc
```

Überprüfe die Installation:

```sh
uv --version
```

## Arbeiten mit uv in Projekten

### Neues Projekt initialisieren

```sh
# Projekt mit pyproject.toml erstellen
uv init my-project
cd my-project
```

### Bestehendes Projekt einrichten

Wenn ein Projekt bereits `pyproject.toml` oder `requirements.txt` hat:

```sh
# Wechsle ins Projekt-Verzeichnis
cd /path/to/project

# Dependencies installieren (erstellt automatisch .venv)
uv sync

# Oder mit requirements.txt
uv pip install -r requirements.txt
```

**Wichtig:** Der `.venv` Ordner sollte in `.gitignore` eingetragen sein:

```gitignore
.venv/
```

### Pakete verwalten

```sh
# Paket hinzufügen
uv add requests

# Paket mit spezifischer Version hinzufügen
uv add "mkdocs-material==9.7.0"

# Development-Paket hinzufügen
uv add --dev pytest

# Paket entfernen
uv remove requests

# Alle Dependencies installieren
uv sync

# Dependencies aktualisieren
uv sync --upgrade
```

### Dependencies prüfen und aktualisieren

```sh
# Veraltete Pakete anzeigen
uv tree --outdated

# Zeigt alle Dependencies mit verfügbaren Updates:
# package v1.0.0 (latest: v1.2.0)

# Alle Pakete auf neueste Version aktualisieren
uv lock --upgrade

# Nur ein bestimmtes Paket aktualisieren
uv lock --upgrade-package mkdocs-material

# Mehrere spezifische Pakete aktualisieren
uv lock --upgrade-package mkdocs-material --upgrade-package requests

# Nach dem Lock: Änderungen installieren
uv sync
```

**Workflow für Updates:**

1. **Veraltete Pakete identifizieren:** `uv tree --outdated`
2. **Lock-File aktualisieren:** `uv lock --upgrade` (alle) oder `uv lock --upgrade-package <paket>` (spezifisch)
3. **Änderungen installieren:** `uv sync`
4. **Testen:** Sicherstellen, dass alles funktioniert
5. **Committen:** Lock-File und ggf. `pyproject.toml` committen

### Dependency Tree anzeigen

```sh
# Vollständiger Dependency-Baum
uv tree

# Nur bestimmte Tiefe anzeigen
uv tree --depth 2

# Reverse Dependencies (wer braucht dieses Paket?)
uv tree --invert requests

# Mit Paketgrößen
uv tree --show-sizes
```

### Python-Skripte ausführen

```sh
# Skript im Virtual Environment ausführen
uv run python script.py

# Oder direkt ein Tool ausführen
uv run mkdocs serve
uv run pytest
```

### Virtual Environment manuell aktivieren

Falls du das Virtual Environment direkt aktivieren möchtest:

```sh
# uv erstellt das Environment automatisch bei Bedarf
source .venv/bin/activate

# Deaktivieren
deactivate
```

## Unterschiede zu klassischem venv/pip

| Aspekt | Klassisch (venv/pip) | Modern (uv) |
|--------|---------------------|-------------|
| Environment erstellen | `python3 -m venv .venv` | Automatisch bei `uv sync` |
| Environment aktivieren | `source .venv/bin/activate` | Nicht nötig mit `uv run` |
| Pakete installieren | `pip install -r requirements.txt` | `uv sync` oder `uv pip install -r requirements.txt` |
| Paket hinzufügen | `pip install package` + manuell zu requirements.txt | `uv add package` |
| Lock-File | Manuell mit pip-tools | Automatisch (`uv.lock`) |
| Geschwindigkeit | Standard | 10-100x schneller |

## Praktisches Beispiel: MkDocs Projekt

```sh
# Projekt klonen
git clone https://gitea.rwcloud.ch/rewa81/mkdocs-wiki.git
cd mkdocs-wiki

# Dependencies installieren (erstellt .venv automatisch)
uv sync

# MkDocs Server starten
uv run mkdocs serve

# HTML-Seiten erstellen
uv run mkdocs build
```

## Häufige Aufgaben

### Python-Version wechseln

```sh
# Spezifische Python-Version verwenden
uv venv --python 3.11
uv venv --python 3.12
```

### Projekt-Dependencies exportieren

```sh
# Für Kompatibilität requirements.txt erstellen
uv pip freeze > requirements.txt
```

### Cache leeren

```sh
uv cache clean
```

## Weiterführende Ressourcen

- [uv Dokumentation](https://docs.astral.sh/uv/)
- [uv GitHub Repository](https://github.com/astral-sh/uv)
- [Migration Guide von pip zu uv](https://docs.astral.sh/uv/pip/)
