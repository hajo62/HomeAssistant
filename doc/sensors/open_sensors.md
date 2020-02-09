# Feinstaub

sensor:
  - platform: rest
    name: Rheinpegel ö
    resource: https://www.pegelonline.wsv.de/webservices/rest-api/v2/stations/2730010.json?includeTimeseries=true
    method: GET
    


    https://www.pegelonline.wsv.de/webservices/rest-api/v2/stations/2730010/W/currentmeasurement.json