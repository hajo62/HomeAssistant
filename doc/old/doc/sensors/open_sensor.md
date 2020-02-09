#
## Feinstaub
Bescheibung der Daten: https://luftdaten.info  
Bescheibung der Integration in HA: https://www.home-assistant.io/components/luftdaten  

`configuration.yaml`:
```
luftdaten:
  sensor_id: 151
  show_on_map: true
  sensors:
    monitored_conditions:
      - P1
      - P2
```

lovelace-card:
```
type: vertical-stack
cards:
  - type: markdown
    title: Feinstaub
    content: ' '
  - type: 'custom:dual-gauge-card'
    title: Zu Hause
    min: 0
    max: 150
    colors:
      - value: 25
        color: '#00FD44'
      - value: 50
        color: '#FFff00'
      - value: 75
        color: '#FFFF00'
      - value: 100
        color: '#FF0000'
      - value: 125
        color: '#CC2EFA'
    inner:
      label: P2
      entity: sensor.luftdaten_11642_p2
    outer:
      label: P1
      entity: sensor.luftdaten_11642_p1
  - type: iframe
    url: 'https://opendata-stuttgart.github.io/feinstaub-map/#12/50.928700/6.911510'
    https://deutschland.maps.luftdaten.info/#15/50.9296/6.9158
    aspect_ratio: 80%
```
