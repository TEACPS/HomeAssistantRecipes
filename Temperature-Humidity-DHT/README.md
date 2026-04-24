# DHT11



sensor:
  - platform: dht
    pin: 
      number: GPIO5
      mode: 
        input: true
        pullup: false
    temperature:
      name: "Living Room Temperature"
    humidity:
      name: "Living Room Humidity"
    update_interval: 5s
    model: DHT11