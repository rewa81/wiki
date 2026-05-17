## Absicherung eines Ubuntu 24.04 VPS in der Cloud

Ein öffentlich zugänglicher VPS erfordert besondere Sicherheitsmaßnahmen, um unautorisierten Zugriff zu verhindern. Hier sind einige wichtige Schritte, um deinen Ubuntu 24.04 VPS in der Cloud abzusichern:

**1. SSH-Zugang:**

* **SSH-Passwort deaktivieren:** Deaktiviere die Anmeldung per Passwort, um Brute-Force-Angriffe zu verhindern. Bearbeite die SSH-Konfigurationsdatei:
   ```bash
   sudo nano /etc/ssh/sshd_config
   ```
   Ändere die Zeile `PasswordAuthentication` zu `no`.

* **SSH-Schlüssel verwenden:** Generiere ein SSH-Schlüsselpaar auf deinem lokalen Rechner und füge den öffentlichen Schlüssel zur Datei `authorized_keys` auf deinem VPS hinzu:
   ```bash
   ssh-copy-id benutzername@deine-vps-ip
   ```
   Ersetze `benutzername` durch deinen Benutzernamen auf dem VPS und `deine-vps-ip` durch die IP-Adresse deines VPS.

* **Root-Anmeldung deaktivieren:** Verhindere die direkte Anmeldung als Root-Benutzer über SSH. In der gleichen `sshd_config` Datei setze `PermitRootLogin` auf `no`.

* **SSH-Port ändern (optional):** Ändere den Standard-SSH-Port (22) auf einen anderen, um automatisierte Angriffe zu erschweren. Beachte, dass du diesen neuen Port bei der Verbindung angeben musst.

**2. Firewall konfigurieren:**

* **UFW (Uncomplicated Firewall) aktivieren:** UFW ist eine benutzerfreundliche Firewall für Ubuntu. Aktiviere sie mit:
   ```bash
   sudo ufw enable
   ```

* **Regeln definieren:** Öffne nur die notwendigen Ports, z.B. für SSH (oder deinen benutzerdefinierten Port) und gegebenenfalls für HTTP/HTTPS, wenn du einen Webserver betreibst. Zum Beispiel:
   ```bash
   sudo ufw allow ssh 
   sudo ufw allow http
   sudo ufw allow https
   ```

* **Standardregeln:** Stelle sicher, dass eingehender Verkehr standardmäßig blockiert ist:
   ```bash
   sudo ufw default deny incoming
   sudo ufw default allow outgoing
   ```

**3. Benutzer und Berechtigungen:**

* **Starke Passwörter:** Verwende für alle Benutzerkonten auf deinem VPS starke, eindeutige Passwörter.
* **Root-Rechte einschränken:** Vermeide es, Routineaufgaben als Root-Benutzer auszuführen. Verwende stattdessen `sudo`, um Befehle mit erhöhten Rechten auszuführen.
* **Unnötige Benutzerkonten löschen:** Entferne alle Benutzerkonten, die nicht benötigt werden.

**4. Regelmäßige Updates:**

* **System aktualisieren:** Halte dein System und alle installierten Pakete auf dem neuesten Stand, um bekannte Sicherheitslücken zu schließen:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

**5. Weitere Sicherheitsmaßnahmen:**

* **Fail2ban installieren:** Fail2ban überwacht Log-Dateien und blockiert automatisch IP-Adressen, die verdächtige Aktivitäten zeigen, wie z.B. wiederholte fehlgeschlagene Anmeldeversuche.
* **Automatische Sicherheitsupdates aktivieren:** Stelle sicher, dass dein System automatisch Sicherheitsupdates installiert.
* **Log-Dateien überwachen:** Überprüfe regelmäßig die Log-Dateien auf verdächtige Aktivitäten.
* **Sicherheitsüberwachungstools verwenden:** Erwäge den Einsatz von Tools wie `rkhunter` oder `chkrootkit`, um dein System auf Rootkits und andere Malware zu überprüfen.

**Zusätzliche Tipps:**

* **Verwende eine starke Passphrase für deinen SSH-Schlüssel.**
* **Ändere regelmäßig deine Passwörter und SSH-Schlüssel.**
* **Beschränke den Zugriff auf deinen VPS auf vertrauenswürdige IP-Adressen, wenn möglich.**
* **Sichere deine Daten:** Erstelle regelmäßig Backups deiner wichtigen Daten.

**Fazit**

Die oben genannten Schritte bieten eine solide Grundlage für die Absicherung deines Ubuntu 24.04 VPS. Beachte jedoch, dass Sicherheit ein kontinuierlicher Prozess ist. Bleibe über neue Sicherheitsbedrohungen informiert und passe deine Sicherheitsmaßnahmen entsprechend an. 

**Disclaimer:** Diese Anleitung bietet grundlegende Sicherheitsmaßnahmen. Für eine umfassende Absicherung können weitere Schritte erforderlich sein, abhängig von den spezifischen Anforderungen und der Nutzung deines VPS. 
