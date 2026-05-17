# Proxmox absichern

## Disable Root Login

Unter Datacenter in der Console einen neuen Benutzer anlegen:

```shell
adduser meinuser
```
Noch das neue Passwort eingeben und nochmals bestätigen.

Danach SUDO installieren:

```shell
apt install sudo -y
```

Nun den neuen User in die SUDO Gruppe aufnehmen:

```shell
sudo usermod -aG sudo meinuser
```
Nun gegehen wir wieder in die Weboberfläche von Proxmox. Unter Datacenter Permissions / User legen wir denselben User an, der wir vorher in der Console erstellt haben.

Mit dem Button "Password" setzen wir für den Benutzer das Passwort.

Nun "Permissions" anklicken und auf den Button "Add" klicken und "User Permissions" auswählen
Als Pfad muss "/" gewählt werden um dem User Root Rechte zu geben. Bei User den neu erstellten User auswählen und als Rolle "Administrator" wählen und die Berechtigungen speichern.

### Als neuer User in Proxmox anmelden
Nun melden wir uns mit dem User root vom der Proxmox Oberfläche ab und logen uns mit dem neuen User ein.

Wenn alles geklappt hat, kann der Root User im Datacenter unter Permissions deaktiviert werden.

### Rootpasswort ablaufen lassen
Mit dem neuen Benutzer im Terminal anmelden. Danach folgenden Commad eingeben:

```shell
sudo passwd -l root
```
es sollte nun eine Meldung kommen "password expiry infomraiton changed". Nun sollte man sich auch per Shell nicht mehr als root anmelden können.

### ssh Config root disablen
In der SSH Config muss der Root User noch deaktiviert werden.

```shell
sudo nano /etc/ssh/ssh_config
``` 

Im File PermitRootLogin yes entfernen resp. auf no stellen und speichern.
Danach den ssh Service neu starten:

```shell
sudo service ssh restart
```


