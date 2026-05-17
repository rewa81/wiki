# Zertifikate mit acme.sh bei letsencrypt lösen

Mit SSH auf den Ansible Server verbinden. Dann im Home Verzeichnis das Git Repo "ansible" im ensprechenden Branch pullen.

Danach die virtuelle Python Umgebung starten.

Das initiale Zertifikat lösen:
```shell
ansible-playbook -i inventory/hosts.yml --vault-password-file ./.vault_pass playbooks/proxmox/manage_certs.yml --tags initial
```

Der Output sollte nun in /certs/ unter der Domain vorhanden sein.


## Troubleshooting

Daten aus dem Zertifikat auslesen:
```shell
## Issuer auslesen
openssl x509 --in ./fullchain.cer -noout -issuer

## Daten auslesen
openssl x509 --in ./fullchain.cer -noout -dates
```
