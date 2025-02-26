Download link :https://programming.engineering/product/lab-4-ece311-introduction-to-linear-control-systems/


# Lab-4-ECE311-Introduction-to-Linear-Control-Systems
Lab 4 – ECE311 Introduction to Linear Control Systems
Position Control of a Permanent Magnet DC motor

MAIN CONCEPTS OF THIS LAB

Control design in the frequency domain: cascade LEAD and PI design

Destabilizing effect of actuator saturation

Integrator antiwindup to mitigate the effects of actuator saturation

INTRODUCTION

In this lab you will design a control system to turn a permanent magnet DC motor into a servomotor. Servomotors are motors with a built-in feedback control loop regulating the shaft

angle to a desired value. RC airplanes use servomotors to control the angle of the ailerons, the rudder, and the elevators. These angles are commanded by the RC pilot via a transmitter. A typical servomotor for this application is illustrated on the side. The snake robot Mamba developed by the NTNU research group of

Kristin Pettersen (see figure below), uses nine servomotors to produce a biologically plausible underwa-ter eel-like motion.

Generally servomotors are used to actuate lightweight robots, such as the robot gymnast below (left-hand side) and the AX12 robot (right-hand side) used in previous-generation laboratories of ECE470.


Recall the simplified model of a PMDC motor developed in Lab 1, with physical parameters summa-

rized in the table below:

Ke Kt

Kt

I θ¨ + b +

θ˙

=

u.

Ra

Ra

Motor Parameter

Description

Numerical value

Ra

Armature resistance

3 ohms

Ke

Back emf constant

0.01

V/(rad/sec)

Kt

Motor torque constant

0.01

N m /A

I

Motor moment of inertia

6 · 10−4 N m /(rad/sec2)

b

Viscous friction coefficient

10−4 N m /(rad/sec)

Suppose that the motor has a constant unknown load, represented by a constant torque τl . The model

becomes

b + Ra

θ˙

=

Ra u +τl .

Iθ¨ +

Ke Kt

Kt

The control objective is to regulate the motor shaft angle θ to a desired constant irrespective of the unknown constant disturbance τl . A typical example where this problem arises is in the position control of an elevator car. Going from floor to floor would correspond to tracking constant reference signals. Being able to control the elevator position irrespective of the number of passengers would correspond to rejecting a constant load disturbance.

Since we want to control the angle of the motor shaft, the output is y = θ. In practice this angle is measured via an optical encoder built into the motor, or a potentiometer in cheaper implementations.

¯

:= (Ra /Kt )τl , we may rewrite the motor model as

Letting d

Iθ¨ +

b +

Ke Kt

θ˙ =

Kt

u + d¯

,

Ra

Ra

where ¯ is a constant unknown disturbance. In the Laplace domain we have the following block diagram, d

Control Design Strategy.

¯

D(s)

=

d

s

Plant

Y(s)

U(s) +

G(s)

+

where

A

G(s) =

,

(1)

s (s + B)

and

A = RatI , B =

b + Ra

/I.

K

Ke Kt

¯

The reference signal is R(s) = θdes /s and the disturbance is D(s) = d/s, where θdes is the desired motor

¯

= (Ra /Kt )τl .

shaft angle and, once again, d


Control specifications. Consider the standard feedback loop depicted below.

()=¯

D s d/s

• Next to meet SPEC 1 without interfering with SPECS 2, 3, you will design a PI controller

C2 (s) =

TI s + 1

, TI>0.

TI s

The final controller will be the cascade connection of the LEAD and PI controllers,

C(s) = C1 (s)C2(s).

• Finally, you will verify whether your design meets SPEC 4.

Lab Overview.

In Section 3 you will design the controller C(s) following the strategy outlined above.

In Section 4 you will analyze the effects of actuator saturation, imposing that the terminal voltage of the motor does not exceeds ± 5 V.

In Section 5 you will learn and implement the technique of integrator antiwindup to mitigate the negative effects of actuator saturation. You will verify that integrator antiwindup significantly improves the performance of the digital controller you implemented in Section 4.

SUBMISSION GUIDELINES AND MARK BREAKDOWN

Marks are assigned to groups. Members of each group get identical marks, unless special circumstances occur. The group lab mark will be made up of three components.

Matlab and Simulink code

4 pts

Video submission

2 pts

Lab report

4 pts

Total

10 pts

Matlab code. Your code should be clean and readable, and it should contain abundant commentary, so that the instructors may follow your development and check its correctness. This component of the mark will be based on the correctness of the code and its readability, and it will be assigned as follows:

present your lab work without scrolling over the code. Rather, you will describe what the lab was about, what were your findings, show the outputs you obtained, and comment on them. In this presentation, you need to show an understanding of the problem you have worked on, and the outputs you’ve pro-duced. If the lab document requests comments on a particular output, these should be an integral part of the discussion. It’s important that each group member contribute equally to the video. This component of the mark will be assigned as follows:

• Define an LTI object G containing the plant transfer function in (1).

Next we design a LEAD controller

C (s) = K

Ts + 1

, 0<α<1, T>0.

1

αTs + 1

The Bode plots of this controller are below.

20 log(|C1 (iω)|) 

√

20 log 1/α

1/T ωmax 1/(αT) log ω

∠(C1 (iω)) 

φmax

1/T ωmax 1/(αT) log ω

The basic properties of this controller are:

1

(2)

ωmax =√

α

max

1

max

1

+ α

φ

= ∠C

(iω

) = sin−1

1

− α

(3)

√

20 log |C1 (iωmax)| = 20 log K + 20 log 1/α.

(4)

Design of the LEAD controller C1 (s). Continue editing your script lab4.m.

• Define a variable initial_crossover and set it equal to 20 rad/sec. Later you will tune it. Choose the gain K such that the gain crossover frequency of KG(s) is initial_crossover. In other words,


you need to find K such that K|G i(initial_crossover) | = 1. Solving the above identity for K and using the command evalfr, define a variable K.

Using the command bode, display the Bode plots of KG(iω) and verify that the magnitude plot crosses 0 dB at ω = initial_crossover rad/sec, as we intended.

Next define a variable alpha with value 0.1. Referring to (3), this will give a peak phase φmax = 54.9 degrees.

Next we need to pick T. We choose it to guarantee that the frequency ωmax where C1 has maximum phase coincides with the crossover frequency of the product C1 (s)G(s), so that the peak phase contribution of C1 (s) is placed at the frequency where it is most needed.

Using (4), we see that at frequency ωmax the magnitude of C1 has increased from its low frequency

√

value by the amount 20 log 1/α. If we want ωmax to be the gain crossover frequency of C1 G, then

√

we look at the frequency ω¯ at which 20 log |KG(iω¯)| = −20 log 1/α, and impose that ωmax = ω¯.

Using (2), we impose

1

T√

= ω¯,

α

which gives

1

T =

√

.

(5)

αω¯

With this choice of T we have ω¯ = ωmax, and at frequency ωmax we have that

20 log |C1 (iωmax)G(iωmax)| = 20 log √|C

1 (iωmax )| + 20 log |G(iωmax )|

= 20 log

1/α + 20 log |KG(iω¯)|

√

√

= 20 log

1/α − 20 log

1/α

= 0 dB.

Therefore ωmax will be the crossover frequency of C1 G, as required.

How do we find the frequency ω¯? By definition, it is the frequency at which 20 log |KG(iω¯)| =

√

−20 log 1/α, or equivalently,

KG(iω¯)/√

= 1,

(6)

α

and therefore ω¯ is the gain crossover frequency of the transfer function KG(s)/√

. Using this

α

idea and the command margin, define a variable omega_bar satisfying identity (6). Next using (5),

define a variable T containing the value of T achieving ωmax = ω¯.

Having found K, α, T, you have designed the LEAD controller C1. Define an LTI object named C1 representing the transfer function C1 (s) just determined.

Using the commands bode and margin, display in one figure the Bode plots of G, those of C1 (s)G(s), and the gain and phase margins of C1 (s)G(s). Verify that the gain crossover frequency of C1 (s)G(s) meets SPEC 2 and that its phase margin meets SPEC 3. Your crossover frequency should be approximately equal to 35 rad/sec and the phase margin should be about 55 degrees. If not, you have an error and you need to fix it.

Output 1. • Produce a list of the parameters you found, and explain in your own words how you determined these parameters. You need to show an understanding of the procedure that you followed to get them, and of why various steps were taken in the design of the LEAD controller.

Display in one figure the Bode plots of G(s), KG(s), and C1 (s)G(s). Explain the effect that the parameter K has on the frequency response of KG(s) when compared to the frequency response of G(s). Then explain the further effect that the LEAD controller has on the frequency response of

G(s)

C1 (s)G(s) when compared to that of KG(s).

• Explain how your LEAD controller meets SPECS 2, 3 but does not yet meet SPEC 1.

Now we design a PI controller to meet SPEC 1,

C2 (s) =

TI s + 1

, TI>0.

TI s

Its Bode plots are below.

20 log(|C2 (iω)|)

− 20dB/decade

0 dB

1/TI

log ω

∠(C2 (iω))

0



π/2

0.1/TI 1/TI 10/TI log ω

At frequencies greater than 10/TI rad/sec, the frequency response of C2 is close to a unit gain (the magnitude is close to 1 and the phase is close to 0). By picking 1/TI much smaller (10 to 100 times, but start with 10 times) than the gain crossover frequency of C1 (s)G(s), we can guarantee that the crossover frequency and phase margin of C1 (s)C2 (s)G(s) will be close to those of C1 (s)G(s).

Design of the PI controller C2 (s) and analysis of the complete design. Continue editing lab4.m.

Choose TI according to the considerations above and define an LTI object named C2 containing the PI controller, and an LTI object named C with the full controller C(s). Determine the gain crossover

frequency and the phase margin of C(s)G(s), and verify that they are both close to ones computed earlier for C1 (s)G(s). In particular, check that SPECS 2, 3 are met.

Now you need to verify whether SPEC 4 has been met. Recall that

1

E(s) = ER (s) + ED (s) = 1 + C(s)G(s) R(s) − 1 + C(s)G(s) D(s).

The frequency response

G(iω)

+ C(iω)G(iω)

determines how the tracking error is affected by a sinusoidal disturbance in steady-state. Display the Bode plots of this frequency response and check whether it is true or false that the magnitude

plot is below the 20 log 0.02 = −34 dB line at all frequencies. You’ll be asked to justify why this is the right criterion to use in checking whether SPEC 4 is met.

Assuming that your controller meets all specifications, the parameters K, T, TI and the initial crossover frequency initial_crossover are now your baseline parameters for tuning. In a mo-ment, you’ll tune various parameters one at a time.

For the standard feedback loop displayed in Section 1 we have that

C(s)G(s)

G(s)

Y(s) =

1 + C(s)G(s) R(s) +

1 + C(s)G(s) D(s) = TR (s)R(s) + TD (s)D(s).

Define two LTI objects T_R and T_D containing the two transfer functions above. Use the command minreal to simplify pole-zero cancellations that will arise in this computation.

Using the command step plot the step responses of thetades*T_R and d_bar*T_D as two subplots in one figure. These are the two contributions of the output y(t) of the standard feedback loop in Section 1 in response to the given reference and disturbance signals.

Using the command stepinfo, determine the settling time and percent overshoot of thetades*T_R.

Output 2. • Display the value of the parameter TI that you’ve chosen, and indicate specifically its relationship to the crossover frequency of C1 G. Comment on the extent to which the PI controller changes the gain crossover frequency and the phase margin of C1 (s)G(s).

• Explain why, in order to check whether SPEC 4 is met, we need to verify whether the magnitude plot of G/(1 + CG) is below the − 34 dB line. Your explanation should rely on the meaning of frequency response, and it should be as clear and precise as possible. Comment: does your controller meet SPEC 4? If it doesn’t, there is an error in your design and you need to fix it.

Produce one figure with the two step responses described earlier as subplots. Comment on these step responses and explain whether or not they confirm that SPEC 1 (asymptotic tracking and disturbance rejection) has been met.

Display the settling time and percent overshoot of y(t) (more precisely, the contribution to it due to r(t)) for your controller.

Parameter tuning and insight.

Starting from the baseline parameters established earlier, vary the PI parameter TI while making sure that 1/TI is 10 to 100 times smaller than the gain crossover frequency of C1 (s)G(s). See how the settling time and percent overshoot of y(t) are affected by TI .

Return to the baseline parameters, and now tune the parameter initial_crossover. This will have the effect of changing the crossover frequency of C(s)G(s). Determine what are the effects of this parameter on the settling time and percent overshoot of y(t), as well as on the maximum magnitude of the frequency response of G/(1 + CG). This latter parameter determines the extent to which sinusoidal disturbances are attenuated.

Output 3. • Describe your tuning of the parameter TI and its effects on settling time and percent overshoot. Is there a clear trend emerging?

To understand the trend you’ve just observed, we need to develop some intuition about the op-eration of PI controllers. We will resort to an electrical analogy. Recall that, in the time do-

main, a PI controller takes the input e(t) and produces the output (assuming K = 1) u(t) = e(t) + (1/TI ) R0t e(τ)dτ. The parameter 1/TI is the weight of the integral term. Think of the in-tegral term as a capacitor, and of the tracking error as the capacitor current. The tracking error charges the capacitor; the greater the tracking error, the faster the capacitor will accumulate charge; if e(t) is positive (or negative) for a long time, the capacitor will have more time to charge. The integrator term, therefore, will make u(t) increase until e(t) changes sign or tends to zero. If e(t) changes sign from positive to negative, then the integrator will begin discharging, but the time it

will take for it to discharge will be longer if the capacitor has a lot of charge to begin with. The pa-rameter 1/TI controls how fast the capacitor charges and discharges in response to a tracking error. If a constant disturbance or constant reference signal causes e(t) to be nonzero, the integrator term

will accumulate “charge” until u(t) is large enough to drive e(t) → 0. This is obviously a beneficial effect. With it, however, there comes a price. Using the intuition just described, explain why more weight on the integral term affects overshoot and settling time in the way you’ve observed.

Describe your tuning of the parameter initial_crossover and its effects on settling time and percent overshoot, and disturbance attenuation. Is there a clear trend emerging?

Clearly, increasing the parameter initial_crossover will increase the gain crossover frequency of C(s)G(s), and hence the bandwidth of the closed-loop transfer function CG/(1 + CG). Using this fact and the relationships seen in class between phase margin and closed-loop system bandwidth on one hand, and overshoot and settling time on the other, explain the trend you observed for settling time and overshoot.

Explaining the trend you observed for disturbance attenuation requires some more thought. The transfer function responsible for disturbance attenuation is G/(1 + CG). The smaller its magnitude, the greater the attenuation. When you increase initial_crossover, what is the primary effect that causes the magnitude of G/(1 + CG) to change in a way consistent with the trend you observed? You might want to investigate this question by investigating the magnitude plot of C.

EFFECTS OF SATURATION

Most if not all actuators are limited in the amount of control effort they can provide. Typically these limits are in the form of a maximum level that the control input is allowed to achieve, in which case we speak of actuator saturation. Often there are further limits on the rate at which the control input is allowed to vary, and those are referred to as rate limits. Control systems must contend with the effects of actuator saturation, and as you shall see in this section these effects can be major, ranging from a degradation of the transient performance of the control loop all the way to a loss of stability, even when the plant itself is LTI!

PMDC motors such as the one investigated in this lab are driven by amplifiers that apply a limited voltage. The Arduino shield, for example, provides a Pulse Width Modulated (PWM) signal of ± 5 Volts to the motor terminals. In this section we will impose such a saturation level on the input u(t), and we will see how your controller performance is affected by it.

Block diagram for actuator saturation. Draw the Simulink diagram displayed below, and name it lab4_1.slx.

value of initial_crossover beyond which the closed-loop system becomes unstable. Find an estimate of this limiting value.

Return to your baseline parameters in lab4.m, and now tune the parameter TI , keeping 1/TI 10 to 100 times smaller than the crossover frequency of C1 (s)G(s). What are the effects of larger integrator gains 1/TI on the settling time calculated over the first five seconds? Once again, you should notice that there is a limiting value of 1/TI beyond which the closed-loop system becomes

unstable. Find an estimate of this limiting value. Observe the control input scope, and notice the effect that larger values of 1/TI have on the length of time that the input is saturated at ±Vlim.

Output 4. • Using the baseline control parameters, produce three figures by saving the scopes (do not take screenshots): the output versus the reference signal, the tracking error, and the control input.

Using the three figures above, comment on how the presence of actuator saturation degrades the settling time of the closed-loop system as compared to your results in Section 3. Observe the control signal u. Why does the saturation have such drastic effects on performance?

Explain what tuning of the parameter initial_crossover you performed, and the effects you noticed on the settling time. What is the limiting value of this parameter before instability ensues? Why does instability occur for large values of initial_crossover? You may want to look at the control signal to gain intuition.

Explain what tuning of the parameter TI you performed, and the effects you noticed on the settling

time. What is the limiting value of this parameter before instability ensues? You should notice that the effects of TI on settling time (and instability) are pronounced. Using the intuition about PI controllers that we developed in Output 3, explain why integral action is problematic in the presence of actuator saturation.

INTEGRATOR ANTIWINDUP

In the previous section you’ve investigated two challenges arising in the presence of actuator saturation. First, “aggressive” controllers will cause a drastic degradation of the transient performance (and even instability) because saturation takes the wind out of their sails, so to speak. If we clip the control to a fixed level, an aggressive controller cannot exert the authority it would need to operate properly. In this context, it’s not a good idea to demand high transient performance.

The second challenge arises due to the presence of integral action in the controller. The integrator of the PI controller gets “charged” by the tracking error even when the control is saturated, and when the tracking error gets small the integrator may still hold a lot of “charge” and it will take a while for it to

discharge before resuming normal operation, causing the control input to stay high and saturated even when the tracking error is small. While the first challenge can be addressed by designing less aggressive controllers, the second challenge is more insidious and requires some thought.

An idea to mitigate the shortcomings of integral action in the presence of actuator saturation is to create a mechanism discharging the integrator state when the controller is saturated, so that when the tracking error gets small so does the control input, and no time is wasted in waiting for the integrator to discharge. A number of techniques exist that do that, and they fall under the umbrella of integrator antiwindup. Here we will investigate the back-calculation algorithm.

Integrator antiwindup. Duplicate your lab4_1.slx diagram and name it lab4_2.slx.

Modify the old diagram to create the diagram below, containing a subsystem named antiwindup controller.

d_bar

G

e

u

output and reference signal

thetades

antiwindup controller

u

tracking error

• The antiwindup controller subsystem is as follows:

Lead controller

PI controller with antiwindup

1

C1

1

e

u

Note that the saturation block has been moved inside the subsystem. The gain Kaw is to be set in your lab4.m code. To begin, set it equal to 1/TI . The controller C(s) has been replaced by the cascade of C1 (s) and a modified PI controller. This modified controller contains a new feedback loop driven by the difference sat(u) − u. This is the antiwindup mechanism. The feedback loop just mentioned discharges the integral state when the control is saturated. To illustrate, suppose that u > Vlim so that the controller is saturated. Then sat(u) − u < 0 and the input to the integrator contains a negative term Kaw(sat(u) − u) that discharges it. An analogous reasoning holds when u < −Vlim. Vice versa, if the control input is below the saturation level, i.e., |u| < Vlim, then

sat(u) − u = 0 and the antiwindup mechanism is disabled. The gain Kaw regulates how quickly the integrator state discharges during saturation. When Kaw = 0 the controller reduces to a standard

PI.

Run the diagram and determine the settling time over the first 5 seconds of operation. Compare the transient performance of this controller to the one you observed in Section 4.

Tune the parameter Kaw and find the value giving the smallest settling time over the first five seconds.

Output 5. • Setting Kaw = 1/TI, produce a figure displaying the tracking error and comment on the settling time your controller achieves over the first five seconds of operation. Compare the performance of the antiwindup controller to that of the baseline controller in Section 4. Has the settling time improved, by how much? What about the percent overshoot? How does the response to the onset of a disturbance at t = 5 seconds compare with and without antiwindup mechanism?

Describe the tuning you performed of the parameter Kaw and display the optimal value you found. Produce a figure with the tracking error and compare it to the result you got in the item above. Has the performance improved by much? Comment on the effects of different values of Kaw on the effectiveness of the antiwindup mechanism.
