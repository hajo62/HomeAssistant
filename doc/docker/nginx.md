# nginx und letsencrypt

Das eigene Heim-Netzwerk muss von *aussen* über IPv4 erreichbar sein ([Vorarbeiten](../fritzbox.md)).  

Eine Beschreibung zur Installation eines nginx-Docker-Container findet sich z.B.  [hier](https://blog.docker.com/2015/04/tips-for-deploying-nginx-official-image-with-docker); hier hatte ich aber Probleme mit der Konfiguration und mit LetsEncrypt. Eine deutlich einfachere Lösung ist die Nutzung eines Containers, der bereits **nginx** und **LetsEncrypt** enthält.  

## Installation des docker-containers mit nginx und letsencrypt
 
 https://letsencrypt.org/de/
 
Ich habe [diesen](https://github.com/linuxserver/docker-letsencrypt) Container verwendet und nach [dieser](https://community.home-assistant.io/t/nginx-reverse-proxy-set-up-guide-docker) Beschreibung vorgegangen.

WICHTIG: homeassistant darf nicht `network_mode: host`in `docker-compose.yaml`-Datei haben; dann scheint der letsencrypt-Container nicht an den HA-Container ran zu kommen...

Nachfolgende `docker-compose.yaml`-Datei configuriert und startet den Container.  

```
  version: '3'
  services:

    letsencrypt:
      image: linuxserver/letsencrypt
      container_name: letsencrypt
      restart: unless-stopped
      cap_add:
      - NET_ADMIN
      volumes:
      - /etc/localtime:/etc/localtime:ro
      - /home/pi/docker/letsencrypt/config:/config
      environment:
      - PGID=1000
      - PUID=1000
      - EMAIL=<my eMail>
      - URL=<myDNS>.duckdns.org
      - SUBDOMAINS=wildcard
      - VALIDATION=duckdns
      - DUCKDNSTOKEN=<my DUCKDNS TOKEN>
      - TZ=Europe/Berlin
      ports:
      - "80:80"
      - "443:443"
```

Der erste Start dauert eine ganze Weile, da das Zertifkat erstellt und heruntergeladen wird. Hier die Ausgabe beim ersten Start:  

```
letsencrypt    | 2048 bit DH parameters present
letsencrypt    | E-mail address entered: <my eMail>
letsencrypt    | duckdns validation is selected
letsencrypt    | Generating new certificate
letsencrypt    | Saving debug log to /var/log/letsencrypt/letsencrypt.log
letsencrypt    | Plugins selected: Authenticator standalone, Installer None
letsencrypt    | Obtaining a new certificate
letsencrypt    | Performing the following challenges:
letsencrypt    | http-01 challenge for <myDNS>.duckdns.org
letsencrypt    | Waiting for verification...
letsencrypt    | Cleaning up challenges
letsencrypt    | IMPORTANT NOTES:
letsencrypt    |  - Congratulations! Your certificate and chain have been saved at:
letsencrypt    |    /etc/letsencrypt/live/<myDNS>.duckdns.org/fullchain.pem
letsencrypt    |    Your key file has been saved at:
letsencrypt    |    /etc/letsencrypt/live/<myDNS>.duckdns.org/privkey.pem
letsencrypt    |    Your cert will expire on 2019-12-23. To obtain a new or tweaked
letsencrypt    |    version of this certificate in the future, simply run certbot
letsencrypt    |    again. To non-interactively renew *all* of your certificates, run
letsencrypt    |    "certbot renew"
letsencrypt    |  - If you like Certbot, please consider supporting our work by:
letsencrypt    |
letsencrypt    |    Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
letsencrypt    |    Donating to EFF:                    https://eff.org/donate-le
```







## ?????
Warum habe ich das notiert?!  

https://medium.com/@pentacent/nginx-and-lets-encrypt-with-docker-in-less-than-5-minutes-b4b8a60d3a71  
https://github.com/Tob1asDocker/rpi-certbot  

```
docker pull tobi312/rpi-certbot
docker run --name mynginx -P -d nginx
```
