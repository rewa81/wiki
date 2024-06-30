# Ausführen von mkdocs
## Option 1: mit DevContainer in VSCode

Voraussetzung: VS-Code mit der Devcontaienr Extension ist installiert, Docker Desktop ist installiert und läuft.

1. Repo klonen
2. In VSCode mit SHIFT+CMD+P  die Funktion "Ordner in Container öffnen" klicken. Danach wir der DEV-Container gebuildet und gestartet.
3. Aufruf der Seite mit http://localhost:8000

## Option 2 - mit Docker-Compose File starten

Voraussetzung: Docker Desktop resp. Docker ist installiert und läuft.

1. Repo klonen
2. Im Terminal in das Root vom Repo gehen.
3. Den Container staten mit ``` docker compose up -d```
4. Aufruf der Seite mit http://localhost:8000

# Konfiguriation
Die Konfiguration befindet sich in der Datei mkdocs.yml und befindet sich im Root des Repos.

# Inhalt der Seite
Der gesamte Inhalt inkl. Multimedia und Scripts befindet sich im Ordner "docs".


