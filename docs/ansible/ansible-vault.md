# Ansible-Vault

## Vault Password File
im Hauptverzeichnis des Repos eine Datei xxxx.vault_pass anlegen (xxxx mit beliebigem Namen ersetzen.
)

## Eine Variable verschlüsseln

Um eine Variable mit Ansible-Vault zu verschlüsseln kann folgender Befehl verwendet werden:

``` sh title="Beispiel Passwort verschlüsseln"
ansible-vault encrypt_string --vault-id rwcloud@rwcloud.vault_pass --encrypt-vault-id rwcloud 'Ki3lahTh' --name 'support_user_password'
```
Damit wird vür die vault rwcloud mit der Password-Datei rwcloud.vault_pass das Passwort Ki3lahTh verschlüsselt.


```
ansible-devcontainer/
├── .devcontainer/
├── .secrets/
├── inventory/
│   ├── group_vars/
│   ├── host_vars/
│   │   ├── vie.yml
│   │   └── parallels.yml
│   └── hosts.yml
├── playbooks/
│   └── test_connection.yml
├── roles/
├── .gitignore
├── inventory-vie.yml
├── readme.md
├── rwcloud.vault_pass
├── vault.yml
└── vie-playbook.yml
```