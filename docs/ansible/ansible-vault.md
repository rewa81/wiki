# Ansible-Vault

## Vault Password File
im Hauptverzeichnis des Repos eine Datei xxxx.vault_pass anlegen (xxxx mit beliebigem Namen ersetzen.
)

## Eine Variable verschlüsseln

Um eine Variable mit Ansible-Vault zu verschlüsseln kann folgender Befehl verwendet werden:

``` sh title="Beispiel Passwort verschlüsseln"
ansible-vault encrypt_string --vault-id [deine-vaultid]@.vault_pass --encrypt-vault-id [deine-vaultid] 'dein passwort' --name 'ansibe-variabelen-name'
```
Damit wird für die die definierte Vault-Id mit der Password-Datei .vault_pass das eingegbene Passwort verschlüsselt.