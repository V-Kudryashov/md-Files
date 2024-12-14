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

This asset contains several physics models that balance in a state of unstable equilibrium. All models balance naturally except Wheelie. In Wheelie's case I had to use magic force.

I tried to make the models and scripts as simple as possible. The controller scripts turned out to be very short and simple. These models and controllers can serve as a basis for your models.

## 2 Models

<a name="bike"></a>
### 2.1 Bike
The bike model consists of two WheelColliders and one Rigidbody. The controller script is very simple, but the formula for BalanceSteer requires some explanation.
The first figure illustrates the relationship between lean angle and lateral acceleration.

![acc](https://github.com/user-attachments/assets/d809ae74-da75-46bb-bd70-8bd78312fe90 | width=100)

a = g * tan(i),

where<br>
i - incline<br>
g - gravity<br>
a - lateral acceleration.<br>

The second figure explains the relationship between lateral acceleration and steering angle.
![steering](https://github.com/user-attachments/assets/c0245026-904f-42ce-9621-c97dad573954)
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

<a name="car"></a>
### 2.3 Car

<a name="mono"></a>
### 2.4 Mono

<a name="segway"></a>
### 2.5 Segway

<a name="wheelie"></a>
### 2.6 Wheelie

