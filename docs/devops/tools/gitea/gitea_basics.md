# Gitea

## Logindaten auf dem Client speichern
Als Voraussetzung muss auf dem Client git installiert sein. Im Terminal können dann foglenden Konfigurationen gesetzt werden:
```sh
# cridential helper aktivieren
git config --global credential.helper store
# git username setzen
git config --global user.name "dein-git-username"
# git emailadresse setzen
git config --global user.email "die-emaildresse-deines-git-benutzers"
```
Danach z.B. Git clone ausführen um ein Repo zu kolnen oder git pull um eine aktuslisieurng des Repos vorzunehmen in dem man aich befindet. Man nuss nochmals den Benutzer und das Passwort eingeben. Danach kann man die Git Befehle ohne Logins ausführen.
