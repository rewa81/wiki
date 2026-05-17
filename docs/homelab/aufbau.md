# Aufbau des Homelabs rwcloud.ch

Dieser Artikel beschreibt den Aufbau meines Homelabs, das als Testumgebung, für das Hosting von Diensten und zum Experimentieren mit neuen Technologien dient.

## Netzwerkstruktur
Das Netzwerk ist in zwei Hauptsubnetze unterteilt:

- 192.168.52.0/24 (Router Netz): Dieses Subnetz beinhaltet die primären Geräte, die direkt mit dem Router verbunden sind.
- 10.10.10.0/24 (Proxmox SDN): Dieses Subnetz wird vom Proxmox Server über SDN (Software Defined Networking) verwaltet und beherbergt die virtuellen Maschinen und Docker-Container.
Die folgende Grafik veranschaulicht die Netzwerkstruktur:

``` mermaid
graph LR
    subgraph "Router Netz"
        Definition(IP-Range - 192.168.52.0/24)
        NAS(NAS - 192.168.52.10) 
        Proxmox[Proxmox - 192.168.52.11]
        Adguard_Extern(Adguard - 192.168.52.13)
    end
    
    subgraph "Proxmox SDN"
        Definition2[IP-Range - 10.10.10.0/24]
        Docker_Base[Docker_Base - 10.10.10.10] 
        Runner01[Runner01 - 10.10.10.11]
        Docker01[Docker01 - 10.10.10.12]
        Adguard_Intern(Adguard - 10.10.10.9)
        Docker_IDP[Docker_IDP - 10.10.10.20]
    end
    
    Proxmox -->|SDN| Docker_Base
    Proxmox -->|SDN| Runner01
    Proxmox -->|SDN| Docker01
    Proxmox -->|SDN| Adguard_Intern
    Proxmox -->|SDN| Docker_IDP

    Adguard_Extern -->|Teilt Netzwerk| Adguard_Intern
```

## Komponenten

- NAS (192.168.52.10): Network Attached Storage für die zentrale Datenspeicherung der Proxmox Backups.
- Proxmox (192.168.52.11): Hypervisor zur Virtualisierung von Servern und Containern.
- Adguard (192.168.52.13 & 10.10.10.9): Netzwerkweiter Werbe- und Trackingschutz. Es gibt eine externe Instanz im Router-Netz und eine interne Instanz im Proxmox SDN.
- Docker_Base (10.10.10.10), Runner01 (10.10.10.11), Docker01 (10.10.10.12), Docker_IDP (10.10.10.20): Verschiedene Docker-Container für unterschiedliche Zwecke (z.B. Anwendungen, CI/CD).
- Router: Standard Router für die Internetverbindung und die Verbindung der Subnetze.

## DNS-Auflösung

Die DNS-Auflösung erfolgt über NextDNS (nextdns.io). NextDNS bietet erweiterte Funktionen wie personalisierte Filterlisten, Analysen und Schutz vor Tracking und Malware. Dies ermöglicht eine flexible und sichere DNS-Konfiguration für das gesamte Netzwerk.

## Externer und interner Zugriff

Für den sicheren externen und internen Zugriff auf das Homelab wird Tailscale verwendet. Tailscale baut ein Mesh-VPN auf, das es ermöglicht, Geräte so zu verbinden, als wären sie im selben lokalen Netzwerk, unabhängig von ihrem physischen Standort. Dies bietet eine einfache und sichere Möglichkeit, auf die Dienste im Homelab zuzugreifen, ohne komplexe Portfreigaben oder VPN-Konfigurationen am Router vornehmen zu müssen.

## Sicherheit

Das SDN ist nur via Port 22 für SSH und 80/443 für HTTP und HTTPS vom Router Netz erreichbar. Alle anderen Ports werden von der Firewall unterbunden. Als Firewall wird die Proxmox Firewall verwendet. 

Als Identity Provider wird Authentik verwendet. Damit wird er Zugriff mittels SSO OAuth2 geregelt.

## Passwort Manager

Alle Zugangsdaten/Secrets sind im Valtwarden (Bitwarden) Passortmager oder in der Ansible-Vault gespeichert.

## Deployment

Das Deployment der Services (in der Regel Docker Container) wird via Ansilbe und Gitea Actions durchgeführt.