# Backup und Restore Gitea
### Einleitung

In dieser Anleitung geht es darum wie man eine Gitea Instanz sichert und restored.

Die Offizielle Anleitung dazu: [https://docs.gitea.com/administration/backup-and-restore#restore-command-restore](https://docs.gitea.com/administration/backup-and-restore#restore-command-restore)

### Gitea Backup

Die Gitea Instanz wird wie folgt gesichert:

1\. Shell Session in den Container starten

```
sudo docker exec -it <gite-container-name> sh
```

2\. Zum git User wechseln und ins Homeverzeichnis des git Users wechseln

```
su git
cd
```

3\. Den Gitea dump ausführen um die Daten zu sichern

```
gitea dump -c /data/gitea/conf/app.ini -d postgres --skip-log
```

4\. Am Schluss des dumps sollte man den Pfad der generierten sehen

[![image.png](https://bookstack.dynasoft.ch/uploads/images/gallery/2024-09/scaled-1680-/pakUszNUx9Un3EHR-image.png)](https://bookstack.dynasoft.ch/uploads/images/gallery/2024-09/pakUszNUx9Un3EHR-image.png)

5\. Danach aus dem Container rausgehen und das File kopieren aus dem Docker container

```
# Container Shell verlassen
CTRL+D (2x)

# Dump File vom Container auf Host kopieren
sudo docker cp <gitea-container-name>:/<pfad_dump_file> <pfad_auf_host>
```

Mit SCP kann das ganze dann noch auf einen Host kopiert werden

Beispiel Command:

```
sudo scp <zip_dump_file>  benutzer@<zielhost>:<ziel_pfad_für_dump>
```

### Gitea Restore

Gitea wird wie folgt wiederhergestellt:

1\. Auf dem Host wird das Dump File entpackt

```
sudo unzip <zip_dump_file>
```

2\. Nun werden die Dateien wie folgt in den Container kopiert

**data Ordner**

```
sudo docker cp data/ <gitea-container-name>:/data/gitea/
```

**repos Ordner**

```
sudo docker cp repos/ <gitea-container-name>:/data/git/repositories/
```

**DB dump in DB Container**

```
sudo docker cp gitea-db.sql <db-container-name>:/tmp/
```

3\. Nach dem kopieren wird die Datenbank restored

```
# Shell in Container starten
sudo docker exec -it <db-container-name> sh

# Restore starten
# $USER und $DATABASE sind in den ENV Variabeln vom DB Container gespeichert (siehe Portainer)
psql -U $USER -d $DATABASE < gitea-db.sql
```

4\. Danach müssen noch die Berechtigungen im Gitea Container geändert werden

```
# Shell in den Gitea Contianer starten
sudo docker exec -it <gite-container-name> sh

# Berechtigungen ändern
cd /
chown -R git:git /data (gitea container)

# Admin Hooks regenerieren
su git
cd
/usr/local/bin/gitea -c '/data/gitea/conf/app.ini' admin regenerate hooks

# Shell Container verlassen
CTRL+D (2x)
```

Jetzt muss der Gitea Container restartet werden damit die Änderungen aktiv werden

### Aufräumen

Nicht vergessen die Zip Files in den Containern und auf dem Host zu löschen sowie die Ordner auf dem Host zu löschen.