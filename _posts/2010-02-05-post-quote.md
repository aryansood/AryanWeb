---
title: "Post: Quote"
categories:
  - Blog
tags:
  - Post Formats
  - quote
mathjax: true
classes: wide
---
​



The quadcopter has a 6 degree of freedom(DOF), which means that we need 6 states to represent its position and orientation in space, but we only have 4 motors which mean to control all the 6 states we have to consider all the forces and momentum applied on the drone. First of all, we should start by introducing the inertia frame with respect to which we express the orientation of the quadcopter. That is not sufficient because apart from the forces we have to consider the torque produced by the 4 rotors, which would be difficult to calculate with respect to the inertial frame and this is why we introduce another frame: the body-fixed frame so we can represent the torque produced that will affect the roll pitch and yaw angles. Now the problem that arises is that we want to translate between the body-fixed frame and the inertial frame, to do so we use the rotational matrix to express vector from the body frame to the inertial frame, and that is important for example when we especially want to calculate the force acting on the drone in the X, Y, Z-axis of the inertial frame but of course in the body-fixed frame, the force is always acting in the Z-axis.

  are the body-fixed frame axes and  are the inertial frame axes.

Now to pass from one frame to the other imaging bringing the body-fixed frame to the inertial frame, and make them coincide at the center, now imaging calculating the angle rotation between the  axis and the axis and called this angle roll and then do the same for the rest of the axis so we will obtain the 3 angles: roll, pitch, and yaw which represent the orientation of the quadcopter(body-fixed frame respect to the initial frame). the matrix representation is this:

Now it is a known fact how to translate between to frame coordinate if we know the angle between 2 correspondent axes so, for example, the angle roll():

and so for the other one is:

Now to find the rotational matrix we just multiply the single rotational matrixes in order, then we will obtain the rotational matrix  to pass from the inertial frame to the body-fixed frame, but we want the other way around so we take the transpose matrix  so we obtain:

Now that we have introduced the Euler angles and how to translate between the 2 coordinate frames let start by introducing the equation of motion that will need to understand the quadcopter position with respect to time and to control our quadcopter.

First of we will introduce some notation:

This represents the angular velocity in the body-fixed frame of the quadcopter.

This represents the orientation( Euler angle) of the quadcopter with respect to the Inertial Frame.

now we can pass on to describe the equation of motion:

First, take into consideration the Gravity Force vector:

the only force acting is in the z position this is why the first and second components of the matrix are zero.

Now we can go on to see how to describe the dynamic of the quadcopter using a set of the differential equation:

So first we see how to express the relation of a vector quantity(force, torque, velocity...) calculated in the body-fixed frame and the inertial frame. To do so we take the time derivative of a vector from in the inertial frame and also in the rotating frame(int this case the quadcopter) the relation for a vector  then we have the relation:

I will not put the proof here but put a link where you can see your self, I highly recommended doing this because apart from understanding better I believe that the derivation is a beautiful mathematical proof using the chain rule and other considerations.

So now we can move and write the equation above using the angular momentum. Consider the angular momentum vector , we know that:

Where  is torque in the inertial frame. We also know that we can write in this way: in the body-fixed frame, where  is the inertial matrix. So considering all the information new have we obtain

.

Now that we have expressed some of the equation valid for all 3-dimensional rigid body in rotation with respect to a fixed frame, let's move to consider better the dynamics of a quadcopter and so the thrust made by the motor and the torque produced.

Let's consider the image above, now you can see clearly that to produce that every single motor contributes to add thrust in the quadcopter and the vector is perpendicular to the single motors.

We call respectively the thrust produce by the first, second, third, and fourth motor and so the first part of the equation of motion can be written as follows, this describes the translational motion by taking account of the thrust produced and the force acting on the quadcopter. We obtain the following:

  where we have to consider the rotation in the roll and pitch angle, as if our quadcopter is rotated we can understand well that the force will be not only in Z direction but also in the X and Y-axis. Now one can ask: well now how the hell I calculate the thrust produced by my motors? Well, experiments have shown that the force produced by the motors follow this relation:

, where is a constant that you have to find experimentally(i will show the septs in the next few posts). Now having discussed the translational motion I still want to remember you that the quadcopter move in 6 DOF, which means that to consider the rotational motion we have to take in consideration the torque produced by each motor. the torque is direct in the yaw axis of rotation, in the pitch angle of rotation, and in the yaw angle of rotation.

Consider the image below:

 $$R_{b1}=\begin{bmatrix} 1 & 0 & 0 \\ 0 & cos(\phi) & sin(\phi) \\ 0 & -sin(\phi)& cos(\phi) \end{bmatrix} $$

Now as we are considering an X type quadcopter, we can see that once we call the arm length than we have    so considering the way that the force is acting we obtain the following

Where represent the torque in the axis. Of course, the constant  has to be found experimentally(in the next post I will show).

Now that we have wrote the first basic equation recall:

Now having in mind that we can obtain , we know the inertia (in the next post I will explain in detail the inertia of a quadcopter which is related to the physical property of the quadcopter) so we can use the differential equation above to get the rate of change of the angular velocity , as to obtain , that we will use to get by numerical methods of solving differential equation(which is, in this case, is the Euler method but you can use another way like the Runge-Kutta which is more precise but more difficult to implement).

So now bringing all the pieces together we will have the following:

From the equation above you can just get   . Later I am going to show from how to get  .

Now another important aspect to consider is that the rate of change of the Euler angles is not equal to angular velocity in the body-fixed frame. Indeed to be able to pass from one another with some sort of rotational matrix, involving only the the  and  angle as you can imagine the does not bring any effect as it is only a "rotation on itself" from the body fixed point of view. The relation(I did not proof because the explanation could go too long, this is why I will put some reference to the ones who are interested also in the derivation). So we have:

from this, after getting the rate of change of Euler angle we can update the value of the angles.

$$\begin{bmatrix} H\cdot k_f\cdot(F_1+F_4-F_2-F_3) \\ H\cdot k_f\cdot(F_1-F_4+F_2-F_3) \\ k_b(\omega_1^2-\omega_2^2+\omega_3^2-\omega_4^2) \end{bmatrix}$$
Now we the required equation that describe the dynamics of a quadcopter, before finishing the post I will explain how to update the angular velocities and Euler angles. In the next post, I will show you how to build a simulator in python.

So now the question is: How I am able to solve the differential equation? Of course, you will have already got that we need a numerical way to solve the differential equation, to do so we will use the Euler method(there are more out there but they are just too complex and sometimes have problem to run in run time as they are heavy and sometimes microcontrollers don't have just enough CPU power and this give us the wrong result as we receive a delay signal respect to the real values).

This below is related to the physical implementation of the equation above.

Now keep in mind the following:

now to solve this we first do so:

then we divide by and so we get :

Now take into consideration the time between two subsequent measures and we update as follows:

after doing so remember equation 11, so after doing the conversion we will get the values

as in a similar way as done above we update the Euler angles:

That's all for this post see you in the next one with the simulation.  See you in the next one!
​
