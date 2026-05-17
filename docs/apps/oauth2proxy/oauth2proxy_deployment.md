# OAuth2-Proxy deployen
!!! danger
    Der OAuth2-Proxy hat keine eigene Rolle. Dieser muss jeweils mit der zu schützenden Applikation mit als eigener Container mit deployed werden!

In der jeweilgen Rolle wird die Verzeichnis-Strutkur in etwa wei folgt aussehen:

```
.
├── readme.md
├── tasks
│   ├── install.yml
│   ├── main.yml
│   └── uninstall.yml
└── vars
    └── service1.yml
```

## Tasks im tasks Verzeinis
Nachfolgend ein Beispiel einer mölgichen konfiguration im tasks Verzeichnis.

```yaml title="main.yml"
---
# service_name setzen
- name: Setze service-samen, wenn tag 'oauth2-service1' vorhanden ist
  ansible.builtin.set_fact:
    service_name: "[service1]"
  when: "['oauth2-service1'] in ansible_run_tags"
  tags:
    - [oauth2-service1]

# Prüfen ob ein Servicename vorhanden ist
- name: Fail if service_name is undefined
  ansible.builtin.fail:
    msg: "service_name is undefined.  You must specify either [oauth2-service1] or [oauth2-service2] tag."
  when: service_name is undefined
  tags:
    - [role-install]
    - [role-uninstall]

# Laden der Variabeln auf gemäss Sertice Name
- name: Lade Variablen für den Service
  ansible.builtin.include_vars:
    file: "vars/{{ service_name }}.yml"
  when: service_name is defined
  tags:
    - [role-install]
    - [role-uninstall]

# Rolle instlalieren mit Docker Compose
- name: [role] installieren
  ansible.builtin.include_tasks:
    file: install.yml
    apply:
      tags:
        [role-install]
  tags:
    - [role-install]

# Rolle deinistallieren
- name: [role] deinstallieren
  ansible.builtin.include_tasks:
    file: uninstall.yml
    apply:
      tags:
        [role-uninstall]
  tags:
    - [role-install]
```

```yaml title="install.yml"
---
- name: Create service1 Docker
  community.docker.docker_compose_v2:
    project_name: mkdocs
    pull: always
    definition:
      services:
        mkdocs:
          image: [image:version]
          container_name: [service1]
          restart: always
          networks:
            - caddy
        oauth2_proxy:
          image: bitnami/oauth2-proxy:latest
          container_name: "{{ mkdocs_container_name }}"
          command: "oauth2-proxy --email-domain=*"
          environment:
            - OAUTH2_PROXY_PROVIDER=oidc
            - OAUTH2_PROXY_CLIENT_ID={{ service1_client_id }}
            - OAUTH2_PROXY_CLIENT_SECRET={{ service1_client_secret }}
            - OAUTH2_PROXY_REDIRECT_URL={{ service1_redirect_url }}
            - OAUTH2_PROXY_OIDC_ISSUER_URL={{ service1_issuer_url }}
            - OAUTH2_PROXY_COOKIE_SECRET={{ service1_cookie_secret }}
            - OAUTH2_PROXY_COOKIE_SECURE=true
            - OAUTH2_PROXY_COOKIE_DOMAINS={{ service1_cookie_domains }}
            - OAUTH2_PROXY_WHITELIST_DOMAINS={{ service1_whitelist_domains }}
            - OAUTH2_PROXY_UPSTREAMS=static://200
            - OAUTH2_PROXY_HTTP_ADDRESS=0.0.0.0:4180
            - OAUTH2_PROXY_SKIP_PROVIDER_BUTTON=true
            - OAUTH2_PROXY_CODE_CHALLENGE_METHOD=S256
            - OAUTH2_PROXY_PASS_AUTHORIZATION_HEADER=true
            - OAUTH2_PROXY_PASS_ACCESS_TOKEN=true
          restart: always
          networks:
            - caddy

      networks:
        caddy:
          name: caddy
          external: true

```

```yaml title="uninstall.yml"
---

- name: Stop service1 docker container
  community.docker.docker_container:
    name: [service1]
    state: [stopped]

- name: Remove service1 docker container
  community.docker.docker_container:
    name: [service1]
    state: absent

- name: Stop oauth-service1 docker container
  community.docker.docker_container:
    name: [oaut-service1]
    state: stopped

- name: Remove oauth-service1 docker container
  community.docker.docker_container:
    name: [oauth-service1]
    state: absent

```

## Variabeln im vars Verzeichnis
Im var Verzeichniss muss für jeden im mail.yml File definierten Verzeichnis ein YML File vorhanden sein. Der Name des Files muss dem definierten Namen gemäss main.yml entsprechen.

```YAML title="service1.yml"
# ACHTUNG: Der Beginn des Varible Name muss identisch sein mit dem Name der Rolle (hier als Beispiel service1). Zudem müssen die Variabel-Name im install.yml identisch sein.
service1_container_name: "gewünschter container name (wird im caddyfile verwendet)"
service1_client_id: "service1-client-id"
service1_client_secret: "service1-client-secret"
service1_cookie_secret: "zufälliger-secret-für-service1"
service1_upstream: "http://service1.example.com"
service1_redirect_url: "https://service1.example.com/oauth2/callback"
service1_issuer_url: "https://authentik.rwcloud.ch/application/o/{servicename}"
service1_whitelist_domains: "example.com"
service1_cookie_domains: "example.com"
```

## Ansible Deploy Beispiel Aufruf mit Tags

```sh title="Ansible Deployment"
# Installation mit dem Tag oauth2-service1 wird das vars File von Service 1 geladen.
ansible-playbook -i inventory/hosts.yml --vault-password-file ./.vault_pass playbooks/proxmox/deploy_docker_services.yml --tags oauth2-service1,service1-install
```