# Addressable LED Strips using ESP RMT

## Device Info
Addressable LED Strips are a nice option to communicate info visually with colored light and require only a simple setup. We use a strip of type WS2812B, which has a 5V power and GND connection, as well as a single signal connection that carries the signal for all LEDs on the strip.

#### Note regarding power: 
LEDs can consume a considerable amount of energy. The longer the LED strip, the more LEDs are in use and the power requirements increase. While for a few LEDs the power may be sources just from the ESPs USB port (which should be able to supply 500mA), for bigger setups you might require a stronger power source than the USB port (and additional info not provided here on how to wire things up).

#### Note regarding the ESP RMT module: 
In this tutorial we'll use the onboard RMT module of the ESP. This is a part of the CPU meant for generating encoded digital pulses which can also be used to generate the data signal for the LED-strip. However, as only one data signal can be generated at a time, the RMT cannot be used to generate IR-codes for IR-remote controlling devices at the same time! So if you want to send IR codes and use the LED strip, you will have to use multiple ESPs. 

## Requirements
* ESP32 C3 Supermini
* WS2812B LED Strip with around 14 LEDs


## Usage
The provided strip only has 14 LEDs, so we will use the USB supplied power of the ESP as power source. 

* Connect the GND line of the strip to the GND pin of the ESP.
* Connect the 5V line of the strip pin to the **5V pin** of the ESP.
* Connect the Data pin of the strip to any GPIO of the ESP. For this tutorial we'll use GPIO 2

TODO
![LED Strip Connections](media/led-strip-connection.jpg)


## Configuration
The light entity is setup as `esp32_rmt_led_strip` platform, which has to be configured according to the used strip type.

```yaml
light:
# The RMT-driven addressable LED strip
# interferes with IR transmitter!

  - platform: esp32_rmt_led_strip
    chipset: WS2812B       # Choose your actual chipset: WS2812/WS2812B/SK6812
    pin: GPIO2            # Use a safe data pin; some boards need to avoid GPIO0
    num_leds: 14          # <-- Set to your strip length
    rgb_order: GRB        # Common for WS2812(B)
    #rmt_channel: 0        # 0–7 on ESP32; use a free channel.
    id: my_rmt_strip
    name: "My RMT LED Strip"
    restore_mode: ALWAYS_OFF   # Optional: start off after boot
    #use_dma: True
```

The strip can be illuminated with a single color a number of different effect. Animated effects include

* a number of [predefined effects](https://esphome.io/components/light/#light-effects)
* custom [lambda-effects](https://esphome.io/components/light/#addressable-lambda-effect) (which are self-defined programs)
* [sACN (a.k.a e131)](https://esphome.io/components/light/#e131-effect): a network protocol to send color data live to the strip from a different data source

Some more are possible (e.g. when integrating with WLED)


```yaml
    # Some built-in effects for testing
    effects:
      - addressable_rainbow:
          name: "Rainbow"
          speed: 20
          width: 14

      - addressable_color_wipe:
          name: "Color Wipe White"
          colors:
            - red: 100%
              green: 100%
              blue: 100%
              num_leds: 5
              gradient: true
            - red: 20%
              green: 0%
              blue: 0%
              num_leds: 5
          add_led_interval: 50ms
          reverse: false

      - e131: 
          universe: 1
          channels: RGB


e131:
  method: UNICAST
```

[Light Automations](https://esphome.io/components/light/#light-automations) allows for customization regarding conditional behaviours.
For example, the following snippet turns the light on when power is provided and sets a predefined effect. 

```yaml

    # Power-on effect (runs on boot)
    on_turn_on:
      - light.turn_on:
          id: my_rmt_strip
          brightness: 50%
          effect: "Rainbow"
```

## Light Settings in Home Assistant