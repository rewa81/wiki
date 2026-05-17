# Ansible-Vault

## Vault Password File

Das Passwordfile lege ich entsprechenden Unterverzeichnis des Verzeichnisses playbooks an. Variable "my secret" mit dem Passwort ersetzen. ACHTUNG: Des Verschlüsselungspasswort im Passwort-Manager ablegen. Da dieses File beim wechseln des Ausführungsservers/Containers manuell erstellt werden muss. Ansonsten können die Ansilbe Vault Variablen nicht mehr entschlüsselt/verschlüsselt werden.

```bash title="Passwortfile erstellen und schützen"
echo "my secret" > .vault_pass

-- Datei noch schützen
chmod 600 .vault_pass
```


## Eine Variable verschlüsseln

Um eine Variable mit Ansible-Vault zu verschlüsseln kann folgender Befehl verwendet werden:

``` sh title="Beispiel Passwort verschlüsseln"
ansible-vault encrypt_string --vault-id [deine-vaultid]@.vault_pass --encrypt-vault-id [deine-vaultid] 'dein passwort' --name 'ansibe-variabelen-name'
```
Damit wird für die die definierte Vault-Id mit der Password-Datei .vault_pass das eingegbene Passwort verschlüsselt.

Wenn man es noch besser machen will und das Passwort/Token nicht in der Bash history erscheinen soll, dann folgenden Command verwenden:

``` sh title="Beispiel Passwort verschlüsseln mit stdin"
ansible-vault encrypt_string --vault-id [deine-vaultid]@.vault_pass --stdin-name 'ansibe-variabelen-name'
```

mit ENTER schein ein Promt wo man den Wert eingeben muss. Mit CTRL+D beendet man den Pompt und verschlüsselt das Passwort.

## Eine Datei verschlüsseln
Mit dem folgenden Befehl wird eine Datei komplett verschüsselt. Als Schlüssel wir die Vault Datei mit dem Passwort verwendet.

``` sh title="Datei verschlüsseln"
ansible-vault encrypt ./playbooks/proxmox/group_vars/all.yml --vault-password-file ./.vault_pass
```

## Eine verschlüsselte Datei entschlüsseln
Um eine Datei zu entschlüsseln verwwendet man den folgenden Befehl:

``` sh title="Datei entschlüsseln"
ansible-vault decrypt ./playbooks/proxmox/group_vars/all.yml --vault-password-file ./.vault_pass
```
WICHTIG: Nach den Änderungen datei wieder verschlüsseln!

## Verschlüselte Datei im Terminal editeren
Mit dem folgenden Befehl öffnet sich die verschlüsselte Datei im Editmodu im Editor Vim.

``` sh title="Datei entschlüsseln"
ansible-vault edit ./playbooks/proxmox/group_vars/all.yml --vault-password-file ./.vault_pass
```
