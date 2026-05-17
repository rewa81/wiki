# Mutual TLS mit Caddy

## Einführung
Dieser Artikel befasst sich mit Mutual TLS und wie es genutzt werden kann, um Homelab-Dienste sicher zu exponieren. TLS ist in der modernen Ära bereits allgegenwärtig und bietet starke symmetrische Verschlüsselung, perfekte Vorwärtsgeheimhaltung und eine öffentliche Vertrauenskette zur Authentifizierung des Servers. Darüber hinaus hat TLS die weniger bekannte Fähigkeit, den Client zu authentifizieren. Durch die Erstellung einer eigenen Zertifizierungsstelle zur Ausstellung von Zertifikaten an Clients kann eine sichere Authentifizierung gegenüber dem Server erreicht werden, wodurch verhindert wird, dass andere Benutzer auf die Webanwendung zugreifen und sie auf Schwachstellen untersuchen.

Dies stellt eine einfachere Lösung dar als die Verwendung eines VPNs, um Dienste zu „exponieren“, solange die App bereits auf TLS angewiesen ist (was mehr Protokolle als nur HTTPS umfasst). Es gibt weniger Benutzerfriktion beim Installieren eines .p12-Zertifikats als beim Einrichten eines VPN-Clients, was wichtig sein könnte, wenn Dienste mit Freunden und Familie geteilt werden.

## Einfache Root-CA erstellen

Dieser Abschnitt richtet eine kleine 2-Schichten-Behörde (Root + Leaf) ein, anstatt der üblichen 3-Schichten-Behörde (Root + Intermediate + Leaf), nur zu Testzwecken. Für alles, was skalierbar sein soll, sollten Sie wahrscheinlich die Smallstep CA verwenden.

Ich verwende auch eine ECDSA-Schlüsselkette, hauptsächlich um zu sehen, wie der Prozess im Vergleich zu RSA aussieht. Daher wird die Root scep384 verwenden. Ich habe scep384 aufgrund seines übertriebenen Sicherheitsniveaus gewählt, das ungefähr einem ~7000-Bit-RSA entspricht.

Wichtiger Hinweis: Ich weiß, es ist verlockend, Bernsteins Kurven (insbesondere ed25519) zu verwenden, aber sie sind vom CA + Browser Forum nicht für die Verwendung in öffentlichen CAs zugelassen und in keinem großen Webbrowser implementiert. Daher habe ich scep256 (was OpenSSL verwirrenderweise prime256 nennt) und scep384 für meine CA gewählt. Natürlich ist auch RSA immer eine Option.

```shell
#Generate private key using scep384:
openssl ecparam -name secp384r1 -genkey -out root.key

#Sign the root certificate
#Pathlen:0 means there can be only one more cert below this CA (no more CAs)
#Make sure you update the subj name with your own names
#C=US is also the country, it's optional
#O= is the organization, also optional
#CN= is the Common Name and it's required
#I also set validity to 4 years, make sure you watch for expiration (manually)
openssl req -new -key root.key -x509 -nodes -days 1461 -out root.pem -subj "/C=US/O=apalrd.net/CN=test" -addext "basicConstraints=critical,CA:TRUE,pathlen:0"

#Now you can view it (for fun)
openssl x509 -in root.pem -text -noout
```

## Benutzerzertifikat mit der Root-CA signieren

Nun können wir OpenSSL verwenden, um ein Benutzerzertifikat zu erstellen und mit dem Root-Schlüssel zu signieren. Ich habe scep256 dafür gewählt, da es einen kleineren „Blast-Radius“ als die Root hat und scep256 immer noch eine ungefähr gleichwertige Sicherheit wie RSA 3072 bietet.

```shell
#Generate scep256 key for this client
openssl ecparam -name prime256v1 -genkey -out adventure@apalrd.net.key

#Generate a CSR (certificate signing request) for my new key
#again, C and O are optional, CN is the Common Name of the cert
openssl req -new -key adventure@apalrd.net.key -out adventure@apalrd.net.csr -subj "/C=US/O=apalrd.net/CN=adventure@apalrd.net" -addext "extendedKeyUsage = serverAuth, clientAuth"

#Sign the CSR using the root
#Sign it allowing for server and client auth as the key usage
openssl x509 -req -in adventure@apalrd.net.csr -CA root.pem -CAkey root.key -CAcreateserial -out adventure@apalrd.net.crt -days 365 -sha256

#Now you can view it (for fun)
openssl x509 -in adventure@apalrd.net.crt -text -noout

#Now let's package it into a P12 archive so you can send it to your favorite client device
#You *must* enter a password here or some OSes will not accept the P12
#The password just encrypts the P12 file itself
openssl pkcs12 -export -out adventure@apalrd.net.p12 -in adventure@apalrd.net.crt -inkey adventure@apalrd.net.key
```

!!! warning
    Ich weiß, es ist verlockend, Bernsteins Kurven (insbesondere ed25519) zu verwenden, aber sie sind vom CA + Browser Forum nicht für die Verwendung in öffentlichen CAs zugelassen und in keinem großen Webbrowser implementiert. Daher habe ich scep256 (was OpenSSL verwirrenderweise prime256 nennt) und scep384 für meine CA gewählt. Natürlich ist auch RSA immer eine Option.

## Benutzerzertifikat mit Smallstep signieren

Wenn Sie bereits eine Smallstep CA aus meinen vorherigen TLS-Videos eingerichtet haben, können Sie diese anstelle von OpenSSL verwenden:

```shell
#Sign cert (run as root on the CA)
#laptop.crt/laptop.key are the key files
#I signed this one for a long ass time
step ca certificate adventure@apalrd.net laptop.crt laptop.key --not-after=2160h 

#Bundle into p12 and include intermediate cert we are using
#The P12 file can be imported into any OS
step certificate p12 laptop.p12 laptop.crt laptop.key --ca /etc/step/certs/intermediate_ca.crt
```

## Caddy für Client Auth konfigurieren

Hier eine Beispiel Caddyfile:

```Caddyfile
# The Caddyfile is an easy way to configure your Caddy web server.

#TLS email (global section)
#Please stop using my email for your Let's Encrypt certs
#I am sick of getting your renewal notices
{
    email mail@example.net
}

#Test website
test1.exampe.com {
    #Caddy example lives here
    root * /usr/share/caddy
    file_server

    #mTLS verify client
    tls {
        client_auth {
            #Default is none
            #there are other options here if you want it to be optional
            #i.e. to bypass a signin page when using mTLS
            mode require_and_verify
            trust_pool file {
                #Can be specified multiple times for multiple roots
                pem_file /etc/caddy/root.pem
            }
        }
    }
}

```
Alternativ kann man das auch mittels Snippet imporiteren:

```Caddyfile
# The Caddyfile is an easy way to configure your Caddy web server.

#TLS email (global section)
#Please stop using my email for your Let's Encrypt certs
#I am sick of getting your renewal notices
{
    email mail@example.net
}

#Snippet for TLS client auth
(tls_client) {
    #mTLS verify client
    tls {
        client_auth {
            #Default is none
            #there are other options here if you want it to be optional
            #i.e. to bypass a signin page when using mTLS
            mode require_and_verify
            trust_pool file {
                #Can be specified multiple times for multiple roots
                pem_file /etc/caddy/root.pem
            }
        }
    }
}

#Test website
test1.example.cp, {
    #Caddy example lives here
    root * /usr/share/caddy
    file_server

    #Reuse the snipper from earlier
    import tls_client
}
```