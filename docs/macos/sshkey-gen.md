# SSH Key erstellen und auf einen Server kopieren

1. SSH-Schlüssel erstellen
Öffnen Sie das Terminal auf Ihrem macOS-System und geben Sie den folgenden Befehl ein, um einen neuen SSH-Schlüssel zu erstellen. Es wird empfohlen, einen RSA-Schlüssel mit einer Länge von 4096 Bits zu verwenden, um eine gute Sicherheit zu gewährleisten:

```sh
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Ersetzen Sie your_email@example.com mit Ihrer E-Mail-Adresse. Dieser Schritt wird dazu verwendet, Ihren öffentlichen Schlüssel zu identifizieren, ist aber nicht zwingend erforderlich.

Sie werden aufgefordert, den Speicherort für den Schlüssel zu wählen (normalerweise ~/.ssh/id_rsa) und ein Passwort einzugeben, um den Schlüssel zu schützen.

2. SSH-Schlüssel auf Ubuntu kopieren
Nachdem Sie den SSH-Schlüssel erstellt haben, können Sie ihn auf Ihre Ubuntu-Maschine kopieren. Dieser Vorgang wird üblicherweise mit dem Befehl ssh-copy-id durchgeführt. Führen Sie im Terminal auf Ihrem macOS-System den folgenden Befehl aus:
```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub username@ubuntu-server
```

Ersetzen Sie username mit dem Benutzernamen auf der Ubuntu-Maschine und ubuntu-server mit der IP-Adresse oder dem Hostnamen des Ubuntu-Servers. Dieser Befehl überträgt Ihren öffentlichen SSH-Schlüssel auf den Ubuntu-Server und fügt ihn zur Datei ~/.ssh/authorized_keys des entsprechenden Benutzers hinzu, wodurch Sie sich zukünftig ohne Passworteingabe anmelden können.

3. Test der SSH-Verbindung
Nachdem Sie den Schlüssel kopiert haben, können Sie testen, ob Sie sich ohne Passwort über SSH verbinden können. Verwenden Sie dazu den folgenden Befehl:

```sh
ssh username@ubuntu-server
```
Wenn alles richtig eingerichtet wurde, sollten Sie ohne Passworteingabe auf den Ubuntu-Server zugreifen können.


# Sicherheitshinweise
- *Schlüssel mit Passwort schützen*: Wählen Sie ein starkes Passwort für Ihren privaten Schlüssel.
- *Regelmäßige Überprüfung*: Überprüfen Sie regelmäßig die Datei ~/.ssh/authorized_keys auf Ihren Servern, um sicherzustellen, dass keine unbefugten Schlüssel vorhanden sind.
- *Einschränkung der SSH-Zugriffsrechte*: Überlegen Sie, ob Sie in der SSH-Konfigurationsdatei (/etc/ssh/sshd_config) Einstellungen wie AllowUsers oder AllowGroups verwenden, um den Zugriff weiter zu beschränken.