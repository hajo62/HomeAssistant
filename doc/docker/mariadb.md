# MariaDB

<img src="https://mariadb.org/wp-content/themes/twentynineteen-child/icons/logo_seal.svg" width="150" border="1">  

Im [Forum](https://community.home-assistant.io/search?q=database%20lock%20) wird immer mal wieder empfohlen, statt der eingebauten Datenbank auf MariaDB zu wechseln. Da ich nun häufiger auch Datenbank-Locks im HomeAssistant-Log beobachtet habe, habe ich nun auf MariaDB auf docker umgestellt.  

## Installation

Aus diesem [Blog](https://www.wouterbulten.nl/blog/tech/home-automation-setup-docker-compose/#mariadb) konnte ich den ersten Wurf für mein `docker-compose.yaml` ableiten. Da mein Datenbank-Container auf dem RPi läuft, nutze ich das Docker-Image von [jsurf](https://hub.docker.com/r/jsurf/rpi-mariadb/).

Hier der entsprechende Teil meiner `docker-compose.yaml`:  

    mariadb:
      image: jsurf/rpi-mariadb
      container_name: mariadb
      restart: unless-stopped
      volumes:
        - /home/pi/docker/mariadb:/var/lib/mysql
        - /home/pi/docker/mariadb:/etc/mysql/conf.d
      environment:
        #- PUID=1000
        #- GUID=1000
        - TZ=Europe/Berlin
        - MYSQL_ROOT_PASSWORD=<root password>
        - MYSQL_USER=pi
        - MYSQL_PASSWORD=<password>
        - MYSQL_DATABASE=homeassistantdb
      ports:
        - 3306:3306

Mit dem Kommando `docker-compose pull mariadb` wird das Image heruntergeladen und mit `docker-compose up [-d] mariadb` gestartet.

Nun noch in der Datei `configuration.yaml` den _Pfad_ zur neuen Datenbank eintragen:

```
...
recorder:
  db_url: mysql://<user>:<password>@<LocalIPAdress>/homeassistantdb?charset=utf8
...  
```

Nach dem Neustart des HomeAssistant-Containers wird die neue (leere) MariaDB genutzt. Die alte History wird nicht übertragen.

### Noch offen

Die Datenbank _gehört_ dem Nutzer `homeassistant:spi`, obwohl ich oben `pi` angegeben habe? - Siehe [hier](https://community.home-assistant.io/t/mariadb-with-docker-compose-db-owner/202197)

Die DB-url als Secret:  
https://community.home-assistant.io/t/mariadb-on-docker-not-addon/184346

## Datenbank-Größe als SQL-Sensor

Um die Größe der Datenbank-Dateien zu bestimmen, wird [hier](https://community.home-assistant.io/t/mariadb-size-almost-2gb-how-to-limit/156220/8) beschrieben, wie man dazu einen [SQL-Sensor](https://www.home-assistant.io/integrations/sql/) nutzen kann.

```
...
  - platform: sql
    db_url: mysql://<user>:<password>@<LocalIPAdress>/homeassistantdb?charset=utf8
    queries:
      - name: 'DataBase size'
        query: 'SELECT table_schema "homeassistantdb", Round(Sum(data_length + index_length) / 1048576, 0) "value" FROM informat$
        column: 'value'
        unit_of_measurement: MB
    scan_interval: 300              # Aktualisieren alle 5 Minuten
...
```    
