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