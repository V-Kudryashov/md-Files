# Balance Collection

**Table of Contents**
- 1 [Description](#1-description)
- 2 [Models](#2-models)
    - 2.1 [Bike](#bike)
    - 2.2 [Biped](#biped)
    - 2.3 [Car](#biped)
    - 2.4 [Mono](#biped)
    - 2.5 [Segway](#biped)
    - 2.6 [Wheelie](#biped)


## 1 Description

This asset contains several physics models that balance in a state of unstable equilibrium. All models balance naturally except Wheelie. In Wheelie's case I had to use magic force.<br>
I tried to make the models and scripts as simple as possible. The controller scripts turned out to be very short and simple. These models and controllers can serve as a basis for your models.<br>
You can open this documentation by clicking the Help button in the Inspector tab of the controller script section.<br>
![Help](https://github.com/user-attachments/assets/6a7a505b-96c3-4c7b-9641-7e95585b8a43)

## 2 Models

<a name="bike"></a>
### 2.1 Bike
The bike model consists of two WheelColliders and one Rigidbody. The controller script is very simple, but the formula for BalanceSteer requires some explanation.
The first figure illustrates the relationship between lean angle and lateral acceleration.

<img src="https://github.com/user-attachments/assets/d809ae74-da75-46bb-bd70-8bd78312fe90" alt="acc" style="width:50%; height:auto;">

a = g * tan(i),

where<br>
i - incline<br>
g - gravity<br>
a - lateral acceleration.<br>

The second figure explains the relationship between lateral acceleration and steering angle.

<img src="https://github.com/user-attachments/assets/c0245026-904f-42ce-9621-c97dad573954" alt="steering" style="width:50%; height:auto;">
Here<br>
O - turn center<br>
C - center of mass<br>
w - wheelbase<br>
S - steering angle<br>
R - rotation radius<br>

then<br>
sin(S/2) = W/2/R<br>
considering that R = V^2/a, we get<br>
sin(S/2) = W/2/V^2 * a<br>
Now we substitute the previously calculated value a<br>
sin(S/2) = W/2/V^2*g*tan(i)<br>
We got the formula that is used in the controller<br>


<a name="biped"></a>
### 2.2 Biped
The Biped consists of three parts - a body and two feet. The feet are moved using a ConfigurableJoint.<br>
Each time the balance is disturbed, the controller moves one of the feet to the balance point.<br>
This type of balance allows the model to not fall, but the model cannot stop, continuing to continuously move her feet. To solve this problem I used magic force. This is the simplest way, but instead of magic force you can replace SphereCollider with BoxCollider and control their rotation.

<a name="car"></a>
### 2.3 Car

The Car model is a car on four wheels. The controller allows the model to balance on two wheels like a bike.

<a name="mono"></a>
### 2.4 Mono

The Mono model is a one-wheeled model. For the wheel I used a SphereCollider since the WheelCollider is not designed to tilt forward or backward.<br>
Longitudinal Balance is achieved by a motor. The wheel rotates until it reaches the balance point.<br>
Lateral balance is achieved using a flywheel with a vertical axis. It's not a completely natural way, but it's simple.

<a name="segway"></a>
### 2.5 Segway

<a name="wheelie"></a>
### 2.6 Wheelie

