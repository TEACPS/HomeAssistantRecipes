# Servo Motor

Unlike a normal motor which only rotates freely, the servo motor can be rotated to a certain position. The user just has to set a position and the motor will move until it has reached it. The control signal is a pulse width modulation (PWM) that will be interpreted by the motor.

See also: [Original Servo component docs](https://esphome.io/components/servo/)

## Connection

The motor requires 3 pins to be connected

* Power (Red) --> 5V
* GND (Brown) --> GND
* PWM Signal (Orange) --> GPIO2


## ESPHome Configuration

To make it work, we first have to setup a GPIO for outputting a PWM signal. This output is referenced by a `servo` component. To control it conveniently, we also setup a `number` component, which will show up as a slider in HA. Using a lambda expression, we map this value to a PWM value that the `servo` component can interpret. 

```yaml
# On ESP32, use ledc to output a PWM signal
output:
  - platform: ledc
    id: pwm_output
    pin: GPIO2
    frequency: 50Hz

# Servo configuration which references the pwm output
servo:
  - id: my_servo
    output: pwm_output
    min_level: 3%     # optional: change from the 3% default to adjust for your servo
    max_level: 15%    # optional: change from the 12% default to adjust for your servo.
                      #           for the servo in the exercise a setting of 15% proved to be good.


# automation for controlling the servo from HA
number:
  - platform: template
    name: Servo Control
    min_value: -100     # left most value
    initial_value: 0    # center value
    max_value: 100      # right most value
    step: 1
    optimistic: true
    set_action:
      then:
        - servo.write:    # when a new value is set, we tell the servo component to move to a new 
                          # position (using the level property)
            id: my_servo
            level: !lambda 'return x / 100.0;'  # lambda bring range from [-100,100] to [-1,1] 
```

To control the servo from HA, a `number` template is used, which will show up in HA as a slider. When a slider value is set in HA, the template calls its `set_action`, which again writes a value to the `servo` component using `servo.write`. The position is derived from the value using a `lambda` expression, which divides the set number by 100 resulting in a value between -1 and 1 (the range of the servo component).