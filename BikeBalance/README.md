# Bike Balance

**Table of Contents**
- 1 [Description](#1-description)
- 2 [Examples](#2-examples)
    - 2.1 [Bike](#bike)
    - 2.2 [Bike Forks](#bike-forks)
- 3 [Scripts](#3-scripts)
    - 3.1 [BikeController](#bikecontroller)
    - 3.2 [ManualControl](#manualcontrol)
    - 3.3 [BikeInput](#bikeinput)
    - 3.4 [DeviceDropdown](#devicedropdown)
    - 3.5 [FrontForkVisualizer](#frontforkvisualizer)
    - 3.6 [RearForkVisualizer](#rearforkvisualizer)

## 1 Description
There are many assets that offer realistic car physics. The [WheelCollider](https://docs.unity3d.com/Manual/class-WheelCollider.html) allows you to simulate the physics of a car quite accurately. It is possible to fine-tune the technical characteristics of the car. What about the bike?
The bike requires balance. This is not an easy task. For this reason, bike models usually use magical forces or no physics at all. Such models move unnaturally. In the Bike Balance asset, balance is maintained solely by changing the steering angle.<br>
Let<br>
> s - steering angle.<br>
t -  target steering angle.<br>
b - balance steering angle.<br>
d - dumper. This value depends on the angle of inclination and the rate of change of this angle. It should be noted that dumper is not a magical power. Dumper is applied not to the bike, but to the steering angle.<br>
k - is a constant coefficient.<br>

Then the steering angle is calculated using the following formula:<br>

s = b + (b - t) * k + d

As a result, the bike tilts towards the turn until the factor (b - t) becomes zero.

## 2 Examples
<a name="bike"></a>
### 2.1 Bike
This example demonstrates the use of a bike physics simulator. The simulator is implemented in the BikeController script. This script requires model with two WheelColiders. This script assumes some control program that sets the target steering angle. In the example provided, this is the ManualControl script. This script uses user input to control the bike, but it could be another program. This program can drive the bike around the race track, around the city, etc.

The ManualControl script has a fullAuto option. If this option is enabled, the controller provides balance as described in the previous paragraph. The user sets the target steering angle using the keyboard, then controller performs the required maneuver while maintaining balance. In this case, driving the bike is very easy.<br>
If fullAuto option turned off:<br>
s = t + d<br>
In this case, maintaining balance is quite difficult. This task is made a little easier by using a dumper. The difficulty is that in order to turn left, you need to turn the steering wheel to the right. After this, the bike will tilt to the left and we can turn left. Such a maneuver seems somewhat paradoxical, but this is exactly what a person does when driving a real bike.

<a name="bike-forks"></a>
### 2.2 Bike Forks
BikeWithForks prefab is similar to Bike prefab. Rear and front forks have been added to the prefab. Two scripts have also been added, FrontForkVisualizer and RearForkVisualizer. Scripts are attached to gameObjects<br>
BikeWithForks->WheelFront->ForkAxis->Fork<br>
BikeWithForks->WheelRear->Fork.<br>
A new gameObject, Wheel_Image, has also been added. Old Wheel_Image has been disabled. 

## 3 Scripts
<a name="bikecontroller"></a>
### 3.1 BikeController
**Description**<br>
BikeController allows you to control a bike, which consists of two WheelColliders and a RigidBody. BikeController itself does not use user input. There are public methods to control the bike: setSteer, SetAcceleration etc. It follows from this that another script is needed to control the bike. This script can use user input, for example.
For a given speed and lean angle, there is a steering angle that ensures the balance of the bike - the balance angle. BikeController uses the balance angle to control the bike. Obviously, the balance angle depends on the mechanical properties of the bike. BikeController was tested on two models close to real ones: cross bike and road bike.

**Properties**
- **FrontCollider** - 
Front [WheelCollider](https://docs.unity3d.com/Manual/class-WheelCollider.html)
- **RearCollider** - 
Rear [WheelCollider](https://docs.unity3d.com/Manual/class-WheelCollider.html)
- **FrontImage** -
Front wheel image (tyre and rim)
- **RearImage** -
Rear wheel image (tyre and rim)
- **MaxSteer** -
Limits steering angle
- **MaxLean** -
Limits bike lean
- **centerOfMassY** -
Determines the height of the center of mass above the ground. Lowering the center of mass makes the bike more stable.
- **useParticle** -
Splashes from under the rear wheel.
- **minTralSlip** -
Wheel slip threshold for the appearance of a trail.
- **minParticleSlip** -
Rear wheel slip threshold for splashing.
- **curves** -
Curves are generated automatically at runtime.
- **info** -
These fields are generated automatically at runtime.

**Public Methods**
- **GetBalanceSteer** -
Returns the balance steering angle.
- **SetAcceleration** -
Sets rear wheel torque acording to the given acceleration.
- **FrontBrake** -
Sets front brake according to the given acceleration. Clamps acceleration to minimize slipping.
- **RearBrake** -
Sets rear brake according to the given acceleration. Clamps acceleration to minimize slipping.
- **SafeBrake** -
Sets both brakes according to the given acceleration. Clamps the brakes to minimize slip and prevent rollovers.
- **ReleaseBrakes** -
Releases both brakes.
- **SetSteerDirectly** -
Sets steering angle to given.
- **setLean** -
Brings the bike closer to the desired lean by slightly off balance. This method is useful for high speed control.
- **setSteer** -
Brings the steer angle closer to the required steer by a small deviation from the balance steer.
- **setSteerByLean** -
Same as setSteer but first calculate lean then call setLean.
- **getSidewaysFriction** -
BikeController changes SidewaysFriction depend on velocity. This method returns SidewaysFriction for the given speed.
- **getLean** -
Returns current lean.
- **getMaxForwardAcceleration** -
Returns max forward acceleration. Acceleration is limited by slipping and the possibility of rolling over.
- **getMaxBrakeAcceleration** -
Returns max brake acceleration. Acceleration is limited by slipping and the possibility of rolling over.
- **getMaxSidewaysAcceleration** -
Returns max sideways acceleration for the given velocity. Acceleration is limited by sideways friction.
- **getMaxVelocity** -
Returns the maximum velocity for a given turning radius.
- **reset** -
Returns bike to the starting position.
- **getup** -
Getting bike up. Use if the bike falls over.
- **getHitPoint** -
Returns the midpoint between the front and back touch points.
<a name="manualcontrol"></a>
### 3.2 ManualControl
<a name="bikeinput"></a>
### 3.3 BikeInput
<a name="devicedropdown"></a>
### 3.4 DeviceDropdown
<a name="frontforkvisualizer"></a>
### 3.5 FrontForkVisualizer
<a name="rearforkvisualizer"></a>
### 3.6 RearForkVisualizer
