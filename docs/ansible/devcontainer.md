# Ansible in einem Devcontainer betreiben
Um Ansible in einem Devcontainer zu betreiben sind folgene Schritte notwendig:

!!! danger "Ansible lint funktioniert nicht - Work in progress"

    Im devcontainer ist Ansible lint nicht funktionstüchtig.

## Zielsetzung:
Die Entwicklung und der Betrieb soll vom Arbeits-PC/Notebook möglichst separiert werden. Die Entwicklung und Ausfürhung der Ansible Befehle wird in VS-Code geschehen. Daher bietet es sich an, Ansible in einem Devcontainer zu betreiben. Damit kann die Abstraktion zur Host-Maschiene sichergestellt werden. Zudem kann auf jedem Rechner auf welchem Docker und VS-Code installiert ist, der Devcontainer betrieben betrieben werden.

Im Devcontainer sollen auch die SSH Keys vorhanden sein um eine SSH Verbindung zu den Remotehosts aufzubauen. Diese werden mit dem decontainer.json File in den Container gemountet.

!!! info "Zusatzfunktion im Devcontainer"

    Im Devcontainer ist zusätzlich noch oh-my-zsh installiert. Das Terminal soll ja schliesslich auch eine "gute Falle" machen. :smile:

## Devcontainer erstellen und starten
### 1. Projektverzeichnis erstellen mit 
```bash title="Shell" 
  mkdir my-ansible-project 
```
### 2. Dockerfile erstellen. Beispiel (Version muss ggf. angepasst werden)
Ordner ".devcontainer" erstellen und im Ordner die Datei "Dockerfile" anlegen.

``` Dockerfile title="Dockerfile"
FROM python:3.12-slim

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    zsh \
    wget \
    curl \
    sudo \
    ansible-lint \
    && apt-get clean

# Create ansible user with zsh as default shell
RUN useradd -ms /bin/zsh ansible

# Switch to ansible user
USER ansible
WORKDIR /home/ansible

# Install Oh-My-Zsh for ansible user
RUN sh -c "$(wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"

# Install zsh-autosuggestions and powerlevel9k
#RUN apt-get install -y zsh-autosuggestions powerlevel9k

# Switch back to root to copy .zshrc and install Ansible
USER root

# Copy a custom .zshrc if you have one
COPY .zshrc /home/ansible/.zshrc

# Change ownership of the home directory
RUN chown -R ansible:ansible /home/ansible

# Install Ansible with matching versions
# RUN pip install ansible==2.14.1 && apt-get install -y ansible=2.14.1

# Set the working directory
WORKDIR /workspace

# Copy the current directory contents into the container at /workspace
COPY . /workspace

# Change default shell to zsh for the container user
USER ansible
SHELL ["/bin/zsh", "-c"]
```
### 3.	Erstellen Sie eine devcontainer.json Datei, um den Devcontainer zu konfigurieren
Ordner ".devcontainer" erstellen und im Ordner die Datei devcontainer.json anlegen.

```json title="devcontainer.json"
{
    "name": "Ansible Devcontainer",
    "build": {
        "dockerfile": "Dockerfile",
        "context": "."
    },
    "workspaceFolder": "/workspace",
    "extensions": [
        "ms-azuretools.vscode-docker",
        "redhat.ansible"
    ],
    "settings": {
        "terminal.integrated.shell.linux": "/bin/zsh"
    },
    "mounts": [
        "source=/pfad/zu/meinem/git-repo/ansible-devcontainer,target=/workspace,type=bind",
        "source=/pfad/zu/meinem/git-repo/ansible-devcontainer/.secrets,target=/home/ansible/.secrets,type=bind" /* ACHTUNG: .secrets ordner muss ausserhalb des .devcontainer Ordner liegen */
    ],
    "remoteUser": "ansible",
    "remoteEnv": {
        "ANSIBLE_VAULT_PASSWORD_FILE": "/home/ansible/.secrets/vie.vault_pass",
        "ANSIBLE_PRIVATE_KEY_FILE": "/home/ansible/.secrets/ansible"
    }
}
```
### Devcontainer starten
Den Ordner des Repos in VSCode öffnen. Wenn der Dialog zum statarten des Devcontainers nicht angezeigt wird kann man mit dem Taskenkombination SHIFT+CMD+P auswählen und "Remote-Containers: Start Remote Container" auswählen.
