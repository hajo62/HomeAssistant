# Feinstaub
    
# Pegelonline

Beim [Pegelonline-Portal](https://www.pegelonline.wsv.de/gast/start) des Wasserstraßen- und Schifffahrtsverwaltung des Bundes lassen sich Pegelstände von Gewässern abfragen.  
[Hier](https://www.pegelonline.wsv.de/webservice/ueberblick) wird eine Rest-API zur Abfrage von Wasserständen bereit gestellt.  
Mit diesem [Aufruf](https://www.pegelonline.wsv.de/webservices/rest-api/v2/stations/2730010/W/currentmeasurement.json) wird der Pegelstand des Rheins für Köln angezeigt. 

Hier nun die Datei `sensors.yaml`:  

```
  # Rheinpegel
  # Get whole JSON object -> Result is String
  - platform: rest
    name: Rheinpegel-Object
    resource: https://www.pegelonline.wsv.de/webservices/rest-api/v2/stations/2730010/W/currentmeasurement.json


  # Pegelstand
  - platform: template
    sensors:
      rheinpegel:
        friendly_name: "Rheinpegel K  ln"
        unit_of_measurement: "m"
        value_template: >
          {% set state = states('sensor.rheinpegel_object') | from_json %}
          {{ state.get('value', 'unknown') | multiply(0.01) }}

  # Pegelstand
  - platform: template
    sensors:
      rheinpegel_trend:
        friendly_name: "Rheinpegel Tendenz"
        value_template: >
          {% set state = states('sensor.rheinpegel_object') | from_json %}
          {{ state.get('trend', 'unknown') }}
```

# Benzinpreise

https://github.com/panbachi/homeassistant-tankerkoenig
https://github.com/panbachi/tankerkoenig-card
https://www.brandeps.com/
https://creativecommons.tankerkoenig.de/TankstellenFinder/index.html