# Bike Lab

The basis of the Bike Lab package is the BikeController script. BikeController implements the physics of the bike and allows you to control it manually or using another program. This program, for example, can drive the bike to a target object (Motobol) or along a track.

Track is the second important part of the Bike Lab package. You can create tracks for motocross, road racing, mountain tracks, city tracks, etc.

The next necessary part is the motorcyclist. The humanoid model is controlled using IK.

The Bike Lab package also includes a Segway Controller. This is a relatively simple script since the balance task for a Segway is much simpler than for a Bike.

**Table of Contents**
- 1 [Bike](#1-bike)
    - 1.1 [Physics](#physics)
        - 1.1.1 [BikeController](#bikecontroller)
    - 1.2 [Input](#input)
        - 1.2.1 [ManualControl](#manualcontrol)
        - 1.2.2 [BikeInput](#bikeinput)
        - 1.2.3 [DeviceDropdown](#devicedropdown)
    - 1.3 [Bike parts](#parts)
        - 1.3.1 [Fork](#fork)
        - 1.3.2 [Swingarm](#swingarm)
        - 1.3.3 [Wheel](#wheel)
        - 1.3.4 [Damper](#damper)

<a name="bike"></a>
## 1 Bike

<a name="physics"></a>
### 1.1 Physics

<a name="bikecontroller"></a>
#### 1.1.1 BikeController
**Description**<br>
BikeController allows you to control a bike, which consists of two WheelColliders and a RigidBody. BikeController itself does not use user input. There are public methods to control the bike: setSteer, SetAcceleration etc. It follows from this that another script is needed to control the bike. This script can use user input, for example.

For a given speed and lean angle, there is a steering angle that ensures the balance of the bike - the balance angle. BikeController uses the balance angle to control the bike. Obviously, the balance angle depends on the mechanical properties of the bike.

Let<br>
> s - steering angle.<br>
t -  target steering angle.<br>
b - balance steering angle.<br>
d - dumper. This value depends on the angle of inclination and the rate of change of this angle. It should be noted that dumper is not a magical power. Dumper is applied not to the bike, but to the steering angle.<br>
k - is a constant coefficient.<br>

Then the steering angle is calculated using the following formula:<br>

s = b + (b - t) * k + d

As a result, the bike tilts towards the turn until the factor (b - t) becomes zero.

**Fields**
- **frontCollider** - 
Front [WheelCollider](https://docs.unity3d.com/Manual/class-WheelCollider.html)
- **rearCollider** - 
Rear [WheelCollider](https://docs.unity3d.com/Manual/class-WheelCollider.html)
- **maxSteer** -
Limits steering angle
- **maxLean** -
Limits bike lean
- **centerOfMassY** -
Determines the height of the center of mass above the ground. Lowering the center of mass makes the bike more stable.
- **curves** -
Curves are generated automatically at runtime.
- **info** -
These fields are calculated automatically at runtime.

**Public Methods**
- **getBalanceSteer** -
Returns the steer angle required to maintain balance at the current speed and the current lean.
- **setAcceleration** -
Sets rear wheel torque acording to the given acceleration.
- **frontBrake** -
Sets front brake according to the given acceleration. Clamps acceleration to minimize slipping.
- **rearBrake** -
Sets rear brake according to the given acceleration. Clamps acceleration to minimize slipping.
- **safeBrake** -
Sets both brakes according to the given acceleration. Clamps the brakes to minimize slip and prevent rollovers.
- **releaseBrakes** -
Releases both brakes.
- **setSteerDirectly** -
Sets steering angle to given.
- **setLean** -
Brings the bike closer to the desired lean by slightly off balance. This method is useful for high speed control.
- **setSteer** -
Brings the steer angle closer to the required steer by a small deviation from the balance steer.
- **setSteerByLean** -
Brings the steer angle closer to the required steer by a small deviation from the balance steer. First calculate lean then call setLean.
- **getSidewaysFriction** -
BikeController changes [SidewaysFriction](https://docs.unity3d.com/ScriptReference/WheelCollider-sidewaysFriction.html) depend on velocity. This method returns [SidewaysFriction](https://docs.unity3d.com/ScriptReference/WheelCollider-sidewaysFriction.html) for the given speed.
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

<a name="input"></a>
### 1.2 Input

<a name="manualcontrol"></a>
#### 1.2.1 ManualControl
**Description**<br>
ManualControl receives data from the [BikeInput](bikeInput) script and controls the [BikeController](bikecontroller) script using apropriate methods.

Input data:
- X axis
- Y axis

Output data:
- Target steering angle
- Forward or brake  acceleration

**Fields**
- **sliderX** -
Optional field. The slider visualizes user input along the X axis.
- **sliderSteer** -
Optional field. The slider visualizes the current steering angle.
- **bikeController** -
[BikeController](#bikecontroller)
- **timeScale** -
Sets the [Time.timeScale](https://docs.unity3d.com/ScriptReference/Time-timeScale.html);
- **maxVelocity** -
Scale of X axis. If X axis = 1 velocity = maxVelocity.
- **fullAuto** -
If true, balance is carried out automatically else the user must balance manually. In the last case, steering angle calculated as mix between user input and balanced steering angle + dumper.
- **autoBalance** -
The interpolation value between user input and balanced steering angle.
- **damper** -
Damper factor.
- **info** -
These fields are calculated automatically at runtime.
<a name="bikeinput"></a>
#### 1.2.2 BikeInput
**Description**<br>
BikeInput supports old and new Input System. If new Input System available user can select one of InputDevece from [InputSystem.devices](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.0/api/UnityEngine.InputSystem.InputSystem.html#properties). BikeInput supports the following devices: [Keyboard](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.0/api/UnityEngine.InputSystem.Keyboard.html), [Mouse](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.0/api/UnityEngine.InputSystem.Mouse.html), [Joystick](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.0/api/UnityEngine.InputSystem.Joystick.html) and [Gamepad](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.0/api/UnityEngine.InputSystem.Gamepad.html).
If new Input System not available BikeInput use [Input.GetAxis](https://docs.unity3d.com/ScriptReference/Input.GetAxis.html) method.
In either case, user input is placed in the xAxis and yAxis fields.

**Fields**
- **keyboardSettings** -
For new input system only.
- **joystickSettings** -
For new input system only.
- **sensitivity** -
User input sensitivity.
- **toZero** -
Determines the speed at which the return to zero occurs.
- **xAxis** -
Output of this script.
- **yAxis** -
Output of this script.

<a name="devicedropdown"></a>
#### 1.2.3 DeviceDropdown
**Description**<br>
For new input system only. This script allow you to select one of the available Input Devices.

**Fields**
- **deviceDropdown** -
This [Dropdown](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Dropdown.html) will be contain available Input Devices.
- **bikeInput** -
[BikeInput](#bikeinput) script.

<a name="parts"></a>
### 1.2 Bike parts

This section contains a description of scripts that carry out the movement of some parts of the bike. Front and rear forks, wheels, etc. Each script must be attached to the corresponding bike model object.
Each script moves several parts of the bike. So FrontFork turns the fork left and right, moves the wheel axle along the fork and rotates the wheel.

There are two ways to connect a visual model to a physical one.
1. Attach visual objects to physical ones.
2. Fill in the script field for visual objects.
If you have attached visual objects, then there is no need to fill out the fields. If you filled in the fields of visual objects, the script will attach them during execution.
The second method is convenient if you edit a visual model in Blender, for example. When you export fbx to an open Unity project, changes immediately appear on the scene. You don't need to re-drag the objects.

The sequence of building a bike model.
1. Place one of the bike prefabs on the scene.
2. Unpack the prefab (not completely).
3. Remove visual objects.
4. Drag the visual model (.fbx file) into your model.
5. Fill in the fields of visual objects in scripts or unpack fbx and drag objects into your model.

<a name="fork"></a>
#### 1.3.1 Fork
**Description**<br>
Fork is designed to visualize a telescopic fork. The [WheelCollider](https://docs.unity3d.com/Manual/class-WheelCollider.html) damper moves vertically, but telescopic fork has some incline. Fork performs appropriate motion of the damper and wheel.

**Fields**
- **frontCollider** -
front [WheelCollider](https://docs.unity3d.com/Manual/class-WheelCollider.html)
- **fork** -
The upper part of the fork.
- **axis** -
The lower part of the fork. The moving part of the dumpers and the wheel axis are located here.
- **frontWheel** -
Front wheel object.
- **forkModel** -
Front fork visual model object.
- **frontAxisModel** -
Front axis visual model object.
- **frontWheelModel** -
Front wheel visual model object.

<a name="swingarm"></a>
#### 1.3.2 Swingarm
**Description**<br>
Swingarm provides the movement of the swingarm and rear wheel.

**Fields**
- **rearCollider** -
Rear [WheelCollider](https://docs.unity3d.com/Manual/class-WheelCollider.html).
- **wheel** -
Rear wheel object.
- **rearWheelModel** -
Rear wheel visual model object.
- **swingarmModel** -
Rear fork visual model object.

<a name="wheel"></a>
#### 1.3.3 Wheel
**Description**<br>
Wheel provides the movement of the wheel. If Children contains a [TrailRenderer](https://docs.unity3d.com/Manual/class-TrailRenderer.html), the script controls it.

**Fields**
- **wheelCollider** -
- **wheelVisualModel** -

<a name="damper"></a>
#### 1.3.4 Damper
**Description**<br>
Dumper provides the movement of the rear dumper.

**Fields**
- **damperBottom** -
Bottom of the damper
- **spring** -
Damper spring
- **springLength** -
Length of the spring
- **modelTop** -
Damper top visual model
- **modelBottom** -
Damper bottom visual model
- **modelSpring** -
Spring visual model
