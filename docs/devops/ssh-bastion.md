# SSH Bastion Host & Reverse Tunneling

Diese Seite beschreibt, wie ein Homelab sicher über einen externen VPS (Bastion Host) erreichbar gemacht werden kann. Dies ermöglicht den Zugriff von verschiedenen Endgeräten (PC, Android, iOS) ohne Portfreigaben am heimischen Router.

## Szenario: Homelab Fernzugriff

Das Ziel ist es, Dienste im lokalen Netzwerk (Proxmox, Docker-Services, Git) über einen Tunnel an einen öffentlich erreichbaren VPS zu binden.

### Architektur Übersicht

1.  **VPS (Bastion Host):** Läuft bei einem beliebigen Provider (z.B. Hetzner, DigitalOcean). Er dient als öffentlicher Einstiegspunkt.
2.  **Homelab (Clients):** Die lokalen Server bauen beim Start aktiv eine Verbindung zum VPS auf (Reverse SSH Tunnel).
3.  **Endgeräte:** Greifen über den VPS auf die getunnelten Dienste zu.

---

## Einrichtung des VPS (Bastion Host)

Der VPS muss "gehärtet" sein, da er direkt aus dem Internet erreichbar ist.

### SSH Konfiguration (`/etc/ssh/sshd_config`)
Um Reverse Tunnels zu ermöglichen und die Sicherheit zu erhöhen:

```ssh
# Reverse Tunneling erlauben
GatewayPorts clientspecified
AllowTcpForwarding yes

# Sicherheit erhöhen
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

### Firewall (UFW)
Nur notwendige Ports öffnen:
- `22`: SSH Zugriff für Admin
- `80/443`: HTTPS für Webdienste
- Spezifische Ports für Gitea/Proxmox (falls direkt exponiert)

---

## Homelab Anbindung (Reverse Tunnel)

Die lokalen Server verbinden sich zum VPS. Hier ein Beispiel-Setup für `autossh`, um die Verbindung stabil zu halten.

### Beispiele für die Dienste

#### 1. Proxmox (Port 8006)
Vom Proxmox-Server zum VPS:
```bash
ssh -N -R 8006:localhost:8006 user@vps-ip
```

#### 2. Docker-Base (Git & SSH)
Hier müssen mehrere Ports getunnelt werden:
- **SSH Linux Update (Port 22):** `ssh -N -R 22222:localhost:22 user@vps-ip` (Port 22222 auf VPS mappt auf 22 lokal)
- **Gitea (Port 2222):** `ssh -N -R 2222:localhost:2222 user@vps-ip`
- **HTTPS (Port 443):** `ssh -N -R 8443:localhost:443 user@vps-ip`

#### 3. Docker-Services (Wiki & SSH)
- **SSH Linux Update (Port 22):** `ssh -N -R 22223:localhost:22 user@vps-ip`
- **HTTPS (Port 443):** `ssh -N -R 9443:localhost:443 user@vps-ip`

---

## Zugriffskontrolle für Dritte (Kollegen & Familie)

Um Kollegen Zugriff auf Git, aber nicht auf Portainer zu geben, nutzen wir unterschiedliche Ansätze auf dem VPS:

### 1. SSH-User Einschränkung
Erstelle für den Kollegen einen eigenen Benutzer auf dem VPS ohne Shell-Zugriff, der nur Tunneling nutzen darf.

### 2. Reverse Proxy (Caddy/Nginx) auf dem VPS
Die sauberste Lösung für Web-Dienste ist ein Reverse Proxy auf dem VPS, der die getunnelten Ports auf Subdomains verteilt.

**Beispiel Caddyfile auf dem VPS:**
```caddy
# Zugriff für alle (Wiki)
wiki.deinedomain.ch {
    reverse_proxy localhost:9443
}

# Zugriff für Kollegen (Git HTTPS)
git.deinedomain.ch {
    reverse_proxy localhost:8443
}

# Admin Bereich (Portainer - nur für dich)
# Zugriff nur via VPN oder spezifischer IP-Whitelist
portainer.deinedomain.ch {
    @admin remote_ip 1.2.3.4 # Deine Home-IP oder VPN-IP
    handle @admin {
        reverse_proxy localhost:8000
    }
    abort
}
```

### 3. SSH Zugriffsschutz
Für den SSH-Zugriff (Port 22/2222) auf Git für den Kollegen:
- Der Kollege erhält einen SSH-Key, der auf dem VPS in der `authorized_keys` des Tunnel-Users hinterlegt ist.
- Mittels `match user` in der `sshd_config` kann man genau festlegen, welche Ports dieser User ansprechen darf (`PermitOpen`).

---

## Zugriffsmethoden für Admins

Neben dem permanenten Reverse Tunnel vom Homelab zum VPS gibt es verschiedene Möglichkeiten, wie du als Admin auf deine internen Ressourcen zugreifen kannst.

### Alternative 1: SSH Jump-Host & Local Forwarding

Wenn du von deinem Laptop (PC/Mac) aus arbeitest, kannst du den Bastion Host als "Sprungbrett" (Jump Host) nutzen. Dies ist oft einfacher als für jeden Dienst einen permanenten Reverse Tunnel zu pflegen, besonders für temporäre Wartungsarbeiten.

Der folgende Befehl verbindet dich über den Bastion Host mit deinen internen Servern und mappt die Ports lokal auf deinen Rechner:

```bash
ssh -J user@bastion-ip \
    -L 2222:backend-server:22 \          # SSH-Zugriff auf Backend-Server
    -L 8080:backend-web:80 \            # HTTP/HTTPS auf Backend-Webserver
    -L 5433:backend-db:5432 \           # PostgreSQL auf Backend-DB
    -N -f -T user@localhost
```

**Erklärung der Parameter:**
- `-J user@bastion-ip`: Definiert den Bastion Host als Jump Host.
- `-L [lokaler-port]:[ziel-host]:[ziel-port]`: Leitet einen lokalen Port auf den Ziel-Host im internen Netz weiter.
- `-N`: Führt keinen Remote-Befehl aus (nur für Port-Forwarding).
- `-f`: Schickt den SSH-Prozess in den Hintergrund.
- `-T`: Deaktiviert die Pseudo-Terminal-Zuweisung.

---

## Alternative 2: WireGuard VPN (Empfohlen für Familie/Kollegen)

Eine moderne und performante Alternative zum SSH-Tunneling ist der Einsatz von **WireGuard**. Hierbei fungiert der Bastion Host als zentraler VPN-Server (Hub), mit dem sich sowohl das Homelab als auch die Endgeräte (Peers) verbinden.

### Architektur Übersicht (WireGuard)

1.  **WireGuard Server (VPS):** Vermittelt den Verkehr zwischen den Peers.
2.  **Homelab Peer:** Verbindet sich permanent mit dem VPS und erlaubt (via Routing) den Zugriff auf das dahinterliegende Subnetz.
3.  **Client Peers (Mobile/PC):** Verbinden sich bei Bedarf mit dem VPS und können über diesen die Dienste im Homelab erreichen.

### Vorteile von WireGuard
- **Performance:** Deutlich schneller und latenzärmer als SSH-Tunneling.
- **Roaming:** Die Verbindung bleibt auf mobilen Geräten stabil, auch wenn das Netz wechselt (WLAN -> 5G).
- **Sicherheit:** Minimaler Angriffsvektor, da WireGuard auf UDP basiert und nicht auf Scans reagiert (Silent).
- **Transparenz:** Sobald der Tunnel steht, können Dienste über ihre internen IPs/Hostnames angesprochen werden, als wäre man im selben Netz.

### Konfigurations-Beispiel VPS (`/etc/wireguard/wg0.conf`)

```ini
[Interface]
PrivateKey = <VPS_PRIVATE_KEY>
Address = 10.0.0.1/24
ListenPort = 51820

# IP-Forwarding aktivieren
# sysctl -w net.ipv4.ip_forward=1

[Peer] # Homelab Server
PublicKey = <HOMELAB_PUBLIC_KEY>
AllowedIPs = 10.0.0.2/32, 192.168.1.0/24 # Routing ins lokale Netz

[Peer] # Kollege / Smartphone
PublicKey = <CLIENT_PUBLIC_KEY>
AllowedIPs = 10.0.0.3/32
```

### Zugriffskontrolle (RBAC) bei WireGuard

Um den Zugriff für Kollegen/Familie einzuschränken (z.B. kein Zugriff auf Portainer), nutzt man die Firewall (**iptables** oder **nftables**) auf dem Bastion Host:

```bash
# Erlaube Kollegen (10.0.0.3) Zugriff auf Git (Port 443 & 22) im Homelab
iptables -A FORWARD -i wg0 -s 10.0.0.3 -d 192.168.1.10 -p tcp --dport 443 -j ACCEPT
iptables -A FORWARD -i wg0 -s 10.0.0.3 -d 192.168.1.10 -p tcp --dport 22 -j ACCEPT

# Blockiere alles andere für den Kollegen
iptables -A FORWARD -i wg0 -s 10.0.0.3 -j DROP
```

---

## Alternative 3: SOCKS5 Proxy via Gluetun (Direkte WireGuard-Anbindung)

In diesem Szenario baut der **Gluetun** Container im Homelab direkt eine WireGuard-Verbindung zum Bastion Host (VPS) auf. Er fungiert als SOCKS5-Proxy für andere Container oder Clients im Netzwerk, wobei der gesamte Traffic durch den WireGuard-Tunnel zum VPS und von dort ins Zielnetz (oder Internet) geleitet wird.

### Architektur Übersicht (Gluetun + WireGuard)

1.  **WireGuard Server (VPS):** Der zentrale Knotenpunkt.
2.  **Gluetun (Homelab):** Verbindet sich als WireGuard-Peer zum VPS.
3.  **SOCKS5-Schnittstelle:** Gluetun stellt einen SOCKS5-Proxy bereit. Clients, die diesen Proxy nutzen, "surfen" mit der Identität und den Zugriffsberechtigungen des Gluetun-Peers über den VPS.

### Konfigurations-Beispiel (Docker Compose im Homelab)

```yaml
services:
  gluetun:
    image: qmcgaw/gluetun
    container_name: gluetun
    cap_add:
      - NET_ADMIN
    environment:
      - VPN_SERVICE_PROVIDER=custom
      - VPN_TYPE=wireguard
      - WIREGUARD_PRIVATE_KEY=<HOMELAB_GLUETUN_PRIVATE_KEY>
      - WIREGUARD_ADDRESSES=10.0.0.2/32
      - WIREGUARD_PUBLIC_KEY=<VPS_PUBLIC_KEY>
      - WIREGUARD_ENDPOINT=vps.deinedomain.ch:51820
      - WIREGUARD_ALLOWED_IPS=10.0.0.0/24, 192.168.1.0/24 # IPs, die über den Tunnel erreichbar sein sollen
      - PROXY_SOCKS5=on
      - PROXY_SOCKS5_PORT=1080
    ports:
      - 1080:1080/tcp # SOCKS5 Proxy lokal im LAN verfügbar machen
    restart: always
```

### Nutzung
- **Interne Clients:** Andere Docker-Container können `gluetun:1080` als Proxy nutzen.
- **Lokale Geräte:** Geräte im heimischen LAN können die IP des Docker-Hosts und Port 1080 als SOCKS5-Proxy eintragen.

---

## Firewall-Steuerung pro Peer (UFW/iptables)

Eine häufige Frage ist, wie man den Zugriff auf dem WireGuard-Server (VPS) so einschränkt, dass verschiedene Peers (Benutzer oder Homelabs) nur auf die für sie bestimmten Ressourcen zugreifen können.

### Grundprinzip: "Default Deny"

Sicherheitstechnisch am besten ist es, jeglichen Traffic zwischen den VPN-Peers und ins Homelab standardmäßig zu verbieten und nur explizite Ausnahmen zu erlauben.

1. **Forwarding in `/etc/default/ufw` konfigurieren:**
   ```bash
   DEFAULT_FORWARD_POLICY="DROP"
   ```

2. **IP-Forwarding im Kernel aktivieren:**
   In `/etc/sysctl.conf`: `net.ipv4.ip_forward=1`.

### Szenario: Mehrere Homelabs & Benutzer-Berechtigungen

Wir haben zwei Homelabs (HL1, HL2) und vier Benutzer mit unterschiedlichen Rollen.

**Netzwerk-Plan (Beispiel):**
- `10.0.0.1`: VPS (WireGuard Hub)
- `10.0.0.10`: Homelab 1 (HL1) Gateway - Subnetz `192.168.1.0/24`
- `10.0.0.20`: Homelab 2 (HL2) Gateway - Subnetz `192.168.2.0/24`
- `10.0.0.101`: Benutzer 1 (Admin HL1)
- `10.0.0.102`: Benutzer 2 (Git User HL1)
- `10.0.0.103`: Benutzer 3 (HL2 User)
- `10.0.0.104`: Benutzer 4 (Cross-Lab Git User)

**Regelwerk in `/etc/ufw/before.rules`:**
Füge diese Regeln vor der `COMMIT` Zeile am Ende des `*filter` Abschnitts ein:

```bash
# --- WireGuard Isolation & RBAC ---

# 1. Homelab Isolation (HL1 und HL2 dürfen nicht miteinander kommunizieren)
# Da Default Forwarding DROP ist, müssen wir hier nichts tun, solange wir 
# keine explizite Regel erlauben, die HL1 nach HL2 routet.

# 2. Benutzer 1: Admin Zugriff auf Homelab 1
-A ufw-before-forward -s 10.0.0.101 -d 192.168.1.0/24 -j ACCEPT

# 3. Benutzer 2: Nur Git (Port 443) in Homelab 1
-A ufw-before-forward -s 10.0.0.102 -d 192.168.1.10 -p tcp --dport 443 -j ACCEPT

# 4. Benutzer 3: Git und Dashy in Homelab 2
-A ufw-before-forward -s 10.0.0.103 -d 192.168.2.10 -p tcp --dport 443 -j ACCEPT
-A ufw-before-forward -s 10.0.0.103 -d 192.168.2.11 -p tcp --dport 8080 -j ACCEPT

# 5. Benutzer 4: Zugriff auf Git in HL1 UND HL2
-A ufw-before-forward -s 10.0.0.104 -d 192.168.1.10 -p tcp --dport 443 -j ACCEPT
-A ufw-before-forward -s 10.0.0.104 -d 192.168.2.10 -p tcp --dport 443 -j ACCEPT

# 6. Rückverkehr erlauben (Established/Related)
-A ufw-before-forward -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
```

### Zusammenfassung der Firewall-Logik
- **Nur WireGuard Traffic:** Durch `DEFAULT_FORWARD_POLICY="DROP"` wird jeglicher Traffic, der nicht explizit erlaubt ist, blockiert.
- **Peer-Isolation:** Da es keine Regel gibt, die Traffic von `10.0.0.10` nach `10.0.0.20` erlaubt, sind die Homelabs voneinander isoliert.
- **Granulare Rechte:** Jeder Benutzer (Peer) wird über seine feste WireGuard-IP identifiziert und erhält nur Zugriff auf die definierten Ziel-IPs und Ports im jeweiligen Homelab.

---

### Alternative: IP-basierte Filterung im Reverse Proxy
Wenn der Zugriff über Web-Dienste erfolgt (wie unter "Zentrale Berechtigungssteuerung" beschrieben), kann auch der Reverse Proxy (Caddy) oder die IAM-Lösung (Authentik) den Zugriff basierend auf der WireGuard-IP des Peers einschränken. Dies ist oft einfacher zu pflegen als Firewall-Regeln auf OS-Ebene.

---

## Mobile Geräte (Android & iOS)

Um von unterwegs zuzugreifen:
1.  **SSH-Clients:** Apps wie **Termius** oder **JuiceSSH** unterstützen SSH-Tunneling nativ.
2.  **Browser:** Einfach über die HTTPS-Subdomains (via Reverse Proxy auf dem VPS).
3.  **VPN (Empfehlung):** Installiere **WireGuard** auf dem VPS. So müssen keine Ports (außer VPN) nach außen geöffnet werden, und die Familie kann sich einfach "ins Netz einwählen".

---

## DNS Konfiguration

Damit die Dienste und der VPN-Server sauber erreichbar sind, müssen entsprechende DNS-Einträge bei deinem Domain-Provider gesetzt werden.

### Variante 1: SSH-Tunnel & Reverse Proxy
Hier zeigt die Domain direkt auf die öffentliche IP des VPS.

| Hostname | Typ | Wert | Zweck |
| :--- | :--- | :--- | :--- |
| `vps.deinedomain.ch` | A | `VPS-IP` | Primärer Hostname des Bastion Hosts |
| `wiki.deinedomain.ch` | CNAME | `vps.deinedomain.ch` | Alias für das Wiki (via Reverse Proxy) |
| `git.deinedomain.ch` | CNAME | `vps.deinedomain.ch` | Alias für Git (via Reverse Proxy) |
| `portainer.deinedomain.ch` | CNAME | `vps.deinedomain.ch` | Alias für Portainer (Admin) |

### Variante 2: WireGuard VPN
Bei WireGuard benötigt primär der VPN-Endpunkt einen festen Namen. Die internen Dienste werden oft über interne IPs oder einen internen DNS angesprochen.

| Hostname | Typ | Wert | Zweck |
| :--- | :--- | :--- | :--- |
| `vpn.deinedomain.ch` | A | `VPS-IP` | Endpoint für die WireGuard-Clients |

**Tipp:** Wenn du innerhalb des VPNs auch Hostnames verwenden möchtest (z.B. `proxmox.home`), kannst du auf dem VPS einen kleinen DNS-Server (wie `dnsmasq` oder `bind9`) betreiben oder die Host-Einträge in der WireGuard-Konfiguration (`PostUp`) mitgeben.

---

## Vergleich & Empfehlung: Was ist die beste Lösung?

Für die Wahl der richtigen Strategie müssen Sicherheit, Wartbarkeit und Benutzerfreundlichkeit abgewogen werden.

### Vergleich der Varianten

| Kriterium | SSH-Tunneling | WireGuard VPN | **Best Practice (Kombiniert)** |
| :--- | :--- | :--- | :--- |
| **Sicherheit** | Hoch (Key-Auth) | Sehr Hoch (UDP Stealth) | **Maximum** (VPN + SSO/MFA) |
| **Einfachheit (Admin)** | Mittel (viele Scripts) | Hoch (Zentrale Config) | **Sehr Hoch** (Einmaliges Setup) |
| **Einfachheit (User)** | Gering (SSH-Kenntnisse) | Mittel (App nötig) | **Sehr Hoch** (Login via Browser) |
| **Zentrale Steuerung** | Komplex (Linux-User) | Mittel (iptables) | **Exzellent** (Web-UI/Authentik) |
| **Flexibilität** | Eingeschränkt | Hoch | **Unbegrenzt** |

### Die "Best Practice" Empfehlung

Wenn das Ziel **maximale Sicherheit bei einfachster Verwaltung** ist, empfehle ich folgende Kombination:

1.  **WireGuard als Backbone:** Der VPS und das Homelab sind permanent via WireGuard verbunden. Das Homelab "versteckt" sich hinter dem VPS.
2.  **Caddy als Reverse Proxy auf dem VPS:** Er nimmt Anfragen für `wiki.deinedomain.ch` etc. entgegen.
3.  **Zentrales Identity Management (z.B. [authentik](../apps/authentik/authentik_einfuehrung.md)):** 
    - Du verwaltest Freunde und Familie an einem zentralen Ort.
    - Berechtigungen werden per Mausklick in einer Weboberfläche erteilt oder entzogen.
    - **Keine neuen Config-Dateien:** Wenn du einem Kollegen Zugriff auf ein neues Tool gibst, muss er nur die entsprechende URL aufrufen und sich mit seinem Account einloggen.

### Zentrale Berechtigungssteuerung (RBAC)

Statt iptables-Regeln auf der Kommandozeile zu schreiben, nutzt du Authentik (oder Zitadel/OAuth2-Proxy), um den Zugriff auf deine Dienste zu steuern.

**Vorteile dieser Lösung:**
- **Single Sign-On (SSO):** Ein Login für alle Dienste (Wiki, Git, Dashy).
- **Multi-Faktor-Authentifizierung (MFA):** Schütze kritische Dienste wie Proxmox oder Portainer zusätzlich mit einem zweiten Faktor (App oder Security Key).
- **Benutzergruppen:** Erstelle Gruppen wie "Familie" (Zugriff auf Wiki & Dashy) und "Dev-Kollegen" (Zugriff auf Git).
- **Sichtbarkeit:** Du siehst im Audit-Log von Authentik genau, wer wann auf welchen Dienst zugegriffen hat.

### DNS & Routing bei Änderungen

Bei dieser Lösung musst du bei Änderungen am Routing oder an Berechtigungen **keine neuen Konfigurationsdateien an die Teilnehmer verteilen**:

- **DNS:** Die Einträge (CNAMEs) zeigen immer auf deinen VPS. Änderungen am Ziel-Server im Homelab erledigst du zentral im Caddyfile auf dem VPS.
- **Berechtigungen:** Möchtest du jemandem den Zugriff entziehen? Deaktiviere einfach den Benutzer in Authentik oder entferne ihn aus der entsprechenden Gruppe. Der Effekt ist sofort aktiv.

---

## Zusammenfassung der Sicherheitstipps
- **Fail2Ban:** Auf dem VPS installieren, um Brute-Force Angriffe zu blockieren.
- **Key-Only Auth:** Niemals Passwörter für SSH erlauben.
- **MFA:** SSH-Zugriff zusätzlich mit 2FA absichern.
- **Auto-Update:** Unattended-Upgrades auf dem VPS aktivieren.
