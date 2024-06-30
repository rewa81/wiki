# Ansible in einem Devcontainer betreiben
Um Ansible in einem Devcontainer zu betreiben sind folgene Schritte notwendig:

## 1. Projektverzeichnis erstellen mit 
```bash title="Shell" 
  mkdir my-ansible-project 
```
## 2. Dockerfile erstellen. Beispiel (Version muss ggf. angepasst werden)
Ordner ".devcontainer" erstellen und im Ordner die Datei "Dockerfile" anlegen.

``` Dockerfile title="Dockerfile"
FROM python:3.12-slim

# Install Ansible
RUN pip install ansible

# Set the working directory
WORKDIR /workspace

# Copy the current directory contents into the container at /workspace
COPY . /workspace
```
## 3.	Erstellen Sie eine devcontainer.json Datei, um den Devcontainer zu konfigurieren
Ordner ".devcontainer" erstellen und im Ordner die Datei devcontainer.json anlegen.

```json title="devcontainer.json"
{
  "name": "Ansible Devcontainer",
  "build": {
    "dockerfile": "Dockerfile",
    "context": ".."
  },
  "workspaceFolder": "/workspace",
  "extensions": [
    "ms-azuretools.vscode-docker",
    "redhat.ansible"
  ],
  "settings": {
    "terminal.integrated.shell.linux": "/bin/bash"
  }
}
```

