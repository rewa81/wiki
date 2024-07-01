# Stuktur und Aufbau des Repos




# Ansible ausführen

Erklärung des nächsten Befehlts:

-i: Pfad zum Hosts-File
--vault-password-file: Pfad und Name des Password-Files für die Ansible Vault.
--limit: Nur dieser Host wird ausgeführt.

``` sh title="Playbook starten"
ansible-playbook -i ../inventory/hosts.yml --vault-password-file ../rwcloud.vault_pass  test_connection.yml --limit testbox
```



