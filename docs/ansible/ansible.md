# Stuktur und Aufbau des Repos

```
ansible-devcontainer/
├── .devcontainer/ # Dev container configuration (e.g., Dockerfile)
├── .secrets/ # Secrets management (e.g., encrypted files)
├── inventory/ # Inventory definition
│   ├── hosts.yml # Main inventory file
│   └── groups/
│       └── group1.yml # Group-specific inventory definitions
│       └── group2.yml # Another group-specific inventory definition
│   └── host_vars/
│       ├── host1.yml
│       └── host2.yml # Host-specific variable files
│
├── playbooks/ # Playbook directory (main entry points for your infrastructure)
│   └── environment-1/ # Environment-specific playbooks
│       ├── deploy.yml
│       └── config.yml
│   └── environment-2/ # Another environment-specific playbook set
│       ├── deploy.yml
│       └── config.yml
│
├── roles/ # Roles directory ( reusable pieces of Ansible code)
│   ├── role1/
│   │   ├── tasks/
│   │   │   └── main.yml
│   │   ├── defaults/
│   │   │   └── main.yml
│   │   └── files/
│   │       └── file
│   └── role2/
│       ├── tasks/
│       │   └── main.yml
│       ├── defaults/
│       │   └── main.yml
│       └── files/
│           └── file
│
├── readme.md # Project documentation and usage information
```


# Ansible ausführen

Erklärung des nächsten Befehlts:

-i: Pfad zum Hosts-File
--vault-password-file: Pfad und Name des Password-Files für die Ansible Vault.
--limit: Nur dieser Host wird ausgeführt.

``` sh title="Playbook starten"
ansible-playbook -i ../inventory/hosts.yml --vault-password-file ../rwcloud.vault_pass  test_connection.yml --limit testbox
```



