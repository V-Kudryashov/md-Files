# Bike Balance

- [Description](#description)
- [Examples](#examples)
    - [Bike](#bike)
    - [Forks](#forks)

## Description
There are many assets that offer realistic car physics. The WheelCollider allows you to simulate the physics of a car quite accurately. It is possible to fine-tune the technical characteristics of the car. What about the bike?
The bike requires balance. This is not an easy task. For this reason, bike models usually use magical forces or no physics at all. Such models move unnaturally. In the Bike Balance asset, balance is maintained solely by changing the steering angle.<br>
Let<br>
s - steering angle.<br>
t -  target steering angle.<br>
b - balance steering angle.<br>
d - dumper. This value depends on the angle of inclination and the rate of change of this angle. It should be noted that dumper is not a magical power. Dumper is applied not to the bike, but to the steering angle.<br>
k - is a constant coefficient.<br>
Then the steering angle is calculated using the following formula:<br>

s = b + (b - t) * k + d

As a result, the bike tilts towards the turn until the factor (b - t) becomes zero.

## Examples
### Bike
This example demonstrates the use of a bike physics simulator. The simulator is implemented in the BikeController script. This script require model with two WheelColiders. This script assumes some control program that sets the target steering angle. In the example provided, this is the ManualControl script. This script uses user input to control the bike, but it could be another program. This program can drive the bike around the race track, around the city, etc.

The ManualControl script has a fullAuto option. If this option is enabled, the controller provides balance as described in the previous paragraph. The user sets the target steering angle using the keyboard, then controller performs the required maneuver while maintaining balance. In this case, driving the bike is very easy.<br>
If fullAuto option turned off:<br>
s = t + d<br>
In this case, maintaining balance is quite difficult. This task is made a little easier by using a dumper. The difficulty is that in order to turn left, you need to turn the steering wheel to the right. After this, the bike will tilt to the left and we can turn left. Such a maneuver seems somewhat paradoxical, but this is exactly what a person does when driving a real bike.

### Forks
Forks prefab is similar to Bike prefab. Rear and front forks have been added to the prefab. Two scripts have also been added, FrontForkVisualizer and RearForkVisualizer. Scripts are attached to gameObjects<br>
BikeWithForks->WheelFront->ForkAxis->Fork<br>
BikeWithForks->WheelRear->Fork.<br>
A new gameObject, Wheel_Image, has also been added. Old Wheel_Image has been disabled. 
