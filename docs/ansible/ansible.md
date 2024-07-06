# Stuktur und Aufbau des Repos

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


# Ansible ausführen

Erklärung des nächsten Befehlts:

-i: Pfad zum Hosts-File
--vault-password-file: Pfad und Name des Password-Files für die Ansible Vault.
--limit: Nur dieser Host wird ausgeführt.

``` sh title="Playbook starten"
ansible-playbook -i ../inventory/hosts.yml --vault-password-file ../rwcloud.vault_pass  test_connection.yml --limit testbox
```



