# Zitadel
## Einfürhung
ZITADEL ist eine moderne Identity- und Access-Management-Plattform (IAM), die es Unternehmen ermöglicht, Benutzeridentitäten sicher zu verwalten, Authentifizierung und Autorisierung zu steuern sowie Single Sign-On (SSO) und Multi-Faktor-Authentifizierung (MFA) zu implementieren. Die Plattform wurde mit einem besonderen Fokus auf Benutzerfreundlichkeit, Skalierbarkeit und nahtlose Integration in moderne Software-Stacks entwickelt.

------
**Wichtige Funktionen von ZITADEL**
	
1.	Benutzer- und Rollenverwaltung
    - ZITADEL ermöglicht eine granulare Steuerung von Zugriffsrechten und die Verwaltung von Benutzergruppen und Rollen.
    - Integration in bestehende Verzeichnisdienste (z. B. LDAP, Active Directory).
2.	Single Sign-On (SSO)
    - Unterstützt die Anmeldung bei verschiedenen Anwendungen mit einer einzigen Benutzeridentität.
    - Ermöglicht die Integration mit Authentifizierungsprotokollen wie OAuth 2.0, OpenID Connect (OIDC) und SAML 2.0.
3.	Multi-Faktor-Authentifizierung (MFA)
    - Erhöht die Sicherheit durch zusätzliche Authentifizierungsfaktoren wie TOTP, SMS oder E-Mail-Codes.
4.	Mandantenfähigkeit
    - Mehrmandantenfähigkeit zur Verwaltung mehrerer Organisationen innerhalb eines Systems, ideal für SaaS-Anwendungen.
5.	APIs und SDKs
    - ZITADEL bietet APIs und SDKs für Entwickler, um die IAM-Funktionen nahtlos in ihre Anwendungen zu integrieren.
6.	Ereignisbasierte Architektur
    - Datenänderungen und Ereignisse werden in einem revisionssicheren Event-Store aufgezeichnet, was Transparenz und Nachvollziehbarkeit erhöht.

## Installation
Die Installation erfolgt via Ansible und Docker. Im Ansible Repo ist eine entsprechende Rolle für die Basis-Installation definiert.

!!! success "DNS Eintrag"
    Der DNS Eintrag bei NextDNS nicht vergessen, sonst kann die URL im Browser nicht augelöst werden.

### Initiale Logindaten

URL: [https://zitadel.rwcloud.ch/ui/console](https://zitadel.rwcloud.ch/ui/console) 
```
Username: admin@zitadel.rwcloud.ch
Passwort: Password1!
```
Das Passwort muss beim 1. Login geändert werden.
