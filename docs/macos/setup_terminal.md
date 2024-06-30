# MacOS Terminal aufsetzen und mit Oh-MyZsh konfigurieren

## Homebrew installieren
Terminal öffnen und folgenden Command ausführen
```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
## Homebrew zum Fahd hinzufügen
Nach der Installation, ACHTUNG: ”[username]” mit dem aktuellen Usernamen ersetzen
```sh
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/[username]/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

## iTerm2 installieren
Für die Installation:
```sh
brew install --cask iterm2
```

## Git installieren
Für die Installaation:
```sh
brew install git
```

## Oh My Zsh installieren
Im Terminal folgende Befehl ausführen:
```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## Istallation PowerLevel10K Theme für Oh My Zsh
Theme herunterladen

```sh
git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
```

Nach dem Download das File ”~/.zshrc” öffnen. und den Wert von “ZSH_THEME" abändern wie unten gezeigt:
```sh
ZSH_THEME="powerlevel10k/powerlevel10k"
```
Dann die Datei speichern und Terminal neu starten oder den folgenden Command ausführen:

```sh
source ~/.zshrc
```

## Meslo Nerd Font installieren
Font installieren mit "y" bestätigen und iTerm2 schliessen.

## Update VSCode TerminalFont (Optional)
Einstellungen öffnen und in Terminal FontFamili den Wert "MesloLGS NF" eintragen oder im settings.json den folgenden Wert hinzufügen:

```json
"terminal.integrated.fontFamily": "MesloLGS NF"
```
## Configure PowerLevel10K
iTerm2 restarten. Man sollte jetzt den PowerLevel10K Configurations-Prozess sehen. Wenn nicht, den Proezss mit dem folgenden Command starten:


```sh
p10k configure
```

Mit den foglenden Einstellungen kann das Terminal angepasst werden.

### Schritgrösse
1. Einstellungen von iTerm2 öffnen
2. Auf Profiles -> Text
3. Schriftgrösse auf ca. 20 stellen

### Wechel der Farbgebung
1. iTerm2 öffnen
2. Download den Theme 
```sh
curl https://raw.githubusercontent.com/josean-dev/dev-environment-files/main/coolnight.itermcolors --output ~/Downloads/coolnight.itermcolors
```
3. Einstellungen von iTerm2 öffnen
4. Auf Profile -> Colors githubusercontent
5. Den heruntergeladenen Theme importieren (coolnight)
6. Das Profil auswählen (collnight)

## ZSH Plugins installieren

Install zsh-augosuggestions:
```sh
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

Install zsh-syntax-highlighting:
```sh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

Öffnen von ”~/.zshrc” und die Plugins hinzufügen. Die Zeile sollte dann so aussehen:
```sh
plugins=(git zsh-autosuggestions zsh-syntax-highlighting web-search)
```

Die Plugins Laden mit dem Command:
```sh
source ~/.zshrc
```

### Fertig!

Quelle: https://www.josean.com/posts/terminal-setup
