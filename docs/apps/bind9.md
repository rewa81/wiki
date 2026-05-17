# BIND9

BIND9 (Berkeley Internet Name Domain Version 9) ist ein Open-Source-[[DNS]] (Domain Name System) Softwaresystem. Es ist die am weitesten verbreitete DNS-Server-Software im Internet und wird vom Internet Systems Consortium (ISC) gepflegt. BIND9 bietet eine robuste und skalierbare Plattform zur Auflösung von Domainnamen in IP-Adressen und umgekehrt und unterstützt erweiterte DNS-Funktionen wie [[DNSSEC]] (DNS Security Extensions), dynamische Updates und inkrementelle Zonentransfers. BIND9 läuft auf einer Vielzahl von Betriebssystemen, einschließlich [[Linux]], [[Unix]] und [[Windows]], und ist hochgradig konfigurierbar und erweiterbar durch die Verwendung von Plugins und Modulen.

Project Homepage: https://www.isc.org/bind/

---
## Installation

ISC stellt ausführbare Dateien für Windows und Pakete für [Ubuntu](../linux/distros/ubuntu.md), [CentOS](../linux/distros/centos.md), [Fedora](../linux/distros/fedora.md) und [Debian](../linux/distros/debian.md) bereit - BIND 9 ESV, Debian - BIND 9 Stable, Debian - BIND 9 Development Version. Die meisten Betriebssysteme bieten auch BIND 9-Pakete für ihre Benutzer an. Diese können mit einer anderen Standardkonfiguration als der Standard-BIND 9-Distribution erstellt worden sein, und einige von ihnen fügen eine eigene Versionsnummer hinzu, die nicht genau der BIND 9-Version entspricht.

### Ubuntu Linux

BIND9 ist im Main-Repository verfügbar. Es muss kein zusätzliches Repository für BIND9 aktiviert werden.

```sh 
sudo apt install bind9
```


### Ubuntu Docker

Als Teil der [Long Term Supported OCI Images](https://ubuntu.com/security/docker-images), bietet Canonical Bind9 als gehärtetes und gewartetes [Docker-Image](../docker/docker.md) an.

```sh
docker run -d --name bind9-container -e TZ=UTC -p 30053:53 ubuntu/bind9:9.18-22.04_beta
```


## Configuration

BIND 9 verwendet eine einzige Konfigurationsdatei namens `named.conf`, die sich typischerweise entweder in `/etc/bind`, `/etc/namedb` oder `/usr/local/etc/namedb` befindet.

Die named.conf besteht aus `logging-` und `options`-Blöcken sowie `category-`, `channel-`, `directory-`, `file-` und `severity-`Anweisungen.

### Named Config

```conf
options {
	...
};

zone "domain.tld" {
	type primary;
	file "domain.tld";
};
```

### Zone File

Je nach Funktionalität des Systems ist eine oder mehrere Zonendateien erforderlich.

```conf
; base zone file for domain.tld
$TTL 2d    ; default TTL for zone

$ORIGIN domain.tld. ; base domain-name

; Start of Authority RR defining the key characteristics of the zone (domain)
@         IN      SOA   ns1.domain.tld. hostmaster.domain.tld. (
                                2022121200 ; serial number
                                12h        ; refresh
                                15m        ; update retry
                                3w         ; expiry
                                2h         ; minimum
                                )

; name server RR for the domain
           IN      NS      ns1.domain.tld.

; mail server RRs for the zone (domain)
        3w IN      MX  10  mail.domain.tld.

; domain hosts includes NS and MX records defined above
; plus any others required
; for instance a user query for the A RR of joe.domain.tld will
; return the IPv4 address 192.168.254.6 from this zone file
ns1        IN      A       192.168.254.2
mail       IN      A       192.168.254.4
joe        IN      A       192.168.254.6
www        IN      A       192.168.254.7

```

#### SOA (Start of Authority)

Ein Start of Authority-Eintrag ist eine Art Ressourceneintrag im Domain Name System ([DNS](../networking/dns.md)) der administrative Informationen über die Zone enthält, insbesondere in Bezug auf Zonentransfers. Das SOA-Eintragsformat ist in RFC 1035 spezifiziert.

```conf
@         IN      SOA   ns1.domain.tld. hostmaster.domain.tld. (
                                2022121200 ; serial number
                                12h        ; refresh
                                15m        ; update retry
                                3w         ; expiry
                                2h         ; minimum
                                )
```


---
## Forwarders (Weiterleitungen)

DNS-Weiterleitungen sind Server, die DNS-Abfragen im Namen eines anderen DNS-Servers auflösen.

Um bind9 als weiterleitenden DNS-Server zu konfigurieren, muss eine `forwarders-`Klausel innerhalb des `options-`Blocks hinzugefügt werden. Die `forwarders-`Klausel gibt eine Liste von IP-Adressen anderer DNS-Server an, an die bind9 Abfragen weiterleiten wird.

```conf
options {
    // ... other options ...
    forwarders {
        8.8.8.8; // Google Public DNS
        1.1.1.1; // Cloudflare DNS
    };
};
```

---
## Access Control (Zugriffskontrolle)

Um Berechtigungen in BIND9 zu konfigurieren, können Sie die “acl”-Anweisung verwenden, um Zugriffskontrolllisten zu definieren, und dann die Anweisungen “allow-query” und “allow-transfer” verwenden, um anzugeben, welche Hosts oder Netzwerke Abfragen oder Zonen übertragen dürfen.

```conf
acl "trusted" {
    192.168.1.0/24;
    localhost;
};

options {
    // ...
    allow-query { any; };
    allow-transfer { "trusted"; };
    // ...
};

zone "example.com" {
    // ...
    allow-query { "trusted"; };
    // ...
};
```

In diesem Beispiel definieren wir eine ACL namens “trusted”, die das Netzwerk 192.168.1.0/24 und den lokalen Host umfasst. Wir geben dann an, dass Hosts in dieser ACL Zonen übertragen dürfen und dass jeder Host Abfragen durchführen darf.

Für die Zone “example.com” geben wir an, dass nur Hosts in der ACL “trusted” Abfragen durchführen dürfen.

Sie können auch andere ACL-Funktionen verwenden, wie “allow-recursion” und “allow-update”, um den Zugriff auf Ihren DNS-Server weiter zu kontrollieren.

---
## Dynamic Updates

Dynamische Updates in BIND ermöglichen die Änderung von DNS-Einträgen in Echtzeit, ohne Zonendateien manuell bearbeiten zu müssen.

### Secure DNS updates with TSIG Key

Dynamische Updates in BIND ermöglichen die Änderung von DNS-Einträgen in Echtzeit, ohne Zonendateien manuell bearbeiten zu müssen.

Um einen TSIG-Schlüssel für die Verwendung mit dynamischen Updates zu erstellen, kann der Befehl `tsig-keygen` verwendet werden.

```
tsig-keygen -a hmac-sha256
```

Um den TSIG-Schlüssel zur Zonenkonfiguration hinzuzufügen, muss die “key”-Anweisung zur “allow-update”-Anweisung in der named.conf-Datei hinzugefügt werden. Zum Beispiel:

```
zone "example.com" {
    type master;
    file "example.com.zone";
    allow-update { key "tsig-key"; };
};
```

In diesem Beispiel verwendet die “allow-update”-Anweisung nun den TSIG-Schlüssel, um Updates für die Zone “example.com” zuzulassen.