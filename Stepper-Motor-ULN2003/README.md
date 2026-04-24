# Stepper Motor with ULN 2003 driver
Stepper motors are motors that can be precisely controlled to turn to a predifined position. The amount of rotation is measured in *steps*, which are usually a tiny fraction of a full rotation. They are therefore well suited for precise positioning tasks.

The module used here is an inexpensive stepper motor with a gearbox that slows down rotation and increases the torque of the motor. It is therefore not suitable for fast rotations, but merely able to do strong but slow movements.

Like all motors, steppers need a lot of power which cannot be sourced from the ESPs GPIO ports. Specialised *driver* chips take the roll of the controller for stronger currents. The module for this tutorial uses a ULN2003 chip, which works by taking 4 control signals to sequentially turn on/off a number of electromagnetic coils inside the motor block, causing the roation of the motor.

TODO
![ULN2003 module with stepper motor in Gearbox]()

## Setup

The setup requires the ULN2003 module to be connected with 6 connections.

* `IN1,2,3,4` need to be connected to specified GPIOs for the control signals
* `VCC`and `GND` pins will be connected to 5V and GND respectively for power supply

TODO
![ULN2003 module connections to the ESP board]()

## Configuration

