# TMCM-Lib – Trinamic Motion Control Module Library for Python

This project aims to offer a clean high-level interface to the TMCM stepper motor controllers by
Trinamic with TMCL firmware.

It currently only supports the module TMCM-3110 but others should be fairly easy to integrate.

## Features

- Object-oriented design
- Documented public elements via type hints and docstrings
- Signaling errors via exceptions
- Blocking and non-blocking moving
- Expressing currents, velocities, and accelerations in physical units
- Reverting of motor direction per motor
- Setting, getting, and moving to coordinates
- Sending heartbeat messages automatically

## Installing

```
pip install TMCM-Lib
```

## Importing

``` python
import tmcm_lib
```

## Examples

### Configuring

``` python
from tmcm_lib import Port, Module

# Constructs the port to which the module is connected.
port = Port('COM1')
# Constructs the module connected to the port.
module = Module.construct(port)
# Enables the pull-up resistors of the limit switches of the module.
module.switch_limit_pullup_enabled = True
# Sets the activity of the limit switches of the module (True = high, False = low).
module.switch_limit_activity = True

# Gets the first motor of the module.
motor = module.motors[0]

# Sets the moving current of the motor in units of milliamperes.
motor.current_moving = 1_000
# Sets the standby current of the motor in units of milliamperes.
motor.current_standby = 100 
# Sets the microstep resolution of the motor.
motor.microstep_resolution = 256
# Sets the velocity for moving the motor in units of fullsteps per second.
motor.velocity_moving = 800
# Sets the acceleration for moving the motor in units of fullsteps per square second.
motor.acceleration_moving = 400
# Enables the left limit switch of the motor.
motor.switch_limit_left.enabled = True
# Enables the right limit switch of the motor.
motor.switch_limit_right.enabled = True
```

### Identifying

``` python
# Prints the model number of the module (e.g. "3110" for TMCM-3110).
print(module.model_number)
# Prints the firmware version of the module (e.g. "(1, 14)" for 1.14).
print(module.firmware_version)
```

### Moving (Blocking)

Blocking moving waits while the motor is moving.

``` python
# Moves the motor relatively by the given distance in units of microsteps.
motor.move_by(512_000)
# Moves the motor absolutely to the given position in units of microsteps.
motor.move_to(0)
# Moves the motor in right direction until stopped (by a limit switch).
motor.move_right()
```

### Moving (Non-blocking)

Non-blocking moving returns immediately after starting the moving.

``` python
# Starts moving the motor relatively by the given distance in units of microsteps.
motor.move_by(512_000, False)
# Waits while the motor is moving.
motor.wait_while_moving()
# Starts moving the motor absolutely to the given position in units of microsteps.
motor.move_to(0, False)
# Waits while the motor is moving.
while motor.moving :
    ...
# Starts moving the motor in right direction until stopped (by a limit switch or stop).
motor.move_right(False)
...
# Stops the motor.
motor.stop()
```

### Motor union

A motor union is a set of motors that move simultaneously.
Motors can move synchronously (i.e. they stop at the same time) or asynchronously.

``` python
from tmcl_lib import MotorUnion

# Constructs a motor union of the first and second motor of the module.
motor_union = MotorUnion(module, [0, 1])

# Moves the motor union synchronously absolutely to the given position in units of microsteps
# (i.e. first motor to first component; second motor to second component).
motor_union.move_to((512_000, 256_000))
```