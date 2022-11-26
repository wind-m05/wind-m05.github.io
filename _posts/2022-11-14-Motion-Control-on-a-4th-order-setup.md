---
title: "Motion Control on a fourth order system"
layout: post
---

![NARX_model](/assets/images/pato-control/pato_real.jpg)
# Summary
The goal was to achieve an error below  $0.006 \; [rad]$ for non-co-located inertia during constant velocity for a setup that had 2 inertias connected via a torsion spring. Firstly, a non-parametric system identification has been done via the direct method such that a controller can be loop-shaped. The first feedback controller has been down-tuned such that sufficient error can be seen. With the error-profile, the feedforward controller has been tuned in an online setting. After the feedforward parameters have been tuned, the error during constant velocity can be reduced by evaluating the power spectral density and shaping the feedback controller accordingly. The final controller achieved an RMS value of $0.0022 \; [rad]
$. Which corresponds to roughly $0.003 \; [rad]$, which is the same as the resolution of the quadrature encoder.

# Contents
1. [Introduction](#introduction)

2. [System Identification](#system-identification)

3. [Weak feedback controller ](#weak-stabilizing-leadlag-controller)

4. [Feedforward tuning](#feedforward-control)

5. [Feedback tuning](#feedback-control)

6. [Error performance](#error-performance)

7. [Conclusion and Discussion](#conclusion-and-discussion)

8. [Resources](#resources)

# Introduction
## Why this project?
This project was part of a course named Control Engineering (4CM00) where the foundations of system identification and controller design have been taught in theory as well as practice.

## Background
The research on 4-th order motion setups have been quite of interest in the control field due to its wide implementations in control problems for companies. Applications that show very similar behavior compared to the set-up in this project are for example wafer-steppers, (3D)-printers, pick and place machines and many more. The setup used in this project is referred as the PATO setup which is provided by the TU/e. There are 2 inertias that are connected via a torsion spring and one of the inertias is connected to an electric motor. A schematic of the setup is shown below.

![NARX_model](/assets/images/pato-control/PATO.png)

Where $J_1$ and $J_2$ are the inertias and $k$ is the stiffness of the torsion spring and $d$ is a damping term, $T_i$ is the input torque created by the motor and both the angles $\theta_1$ and $\theta_2$ can be measured via [quadrature encoders](https://en.wikipedia.org/wiki/Incremental_encoder) which have a resolution of $0.003 \; [rad]$.

## Constraints
- The design of electrical circuitry and software implementations of communications between computer and setup are all excluded from this project.
- A third order reference profile of the following form must be tracked.

![NARX_model](/assets/images/pato-control/reference.png)

## Goal 
- The maximal allowable error during constant velocity of $\omega=35 \;[rad/s] $ must be under $0.006\; [rad]$.

# System Identification
## First principles modeling
From first principles modeling, a 4-th order transfer function can be found for both the co-located and non-co-located inertias. The co-located inertia is given by $J_1$ in the diagram, because it is at the same spot as actuation, while the non-co-located inertia is given by $J_2$ in the diagram because it is not at the same position as actuation.

The transfer function for the co-located inertia is given by:

$$ \frac{\theta_1}{T_i} = \frac{J_2 s^2 + d s + k}{J_1 J_2 s^4 + (J_1 + J_2) d s^3 + (J_1 + J_2) ks^2} $$


The transfer function for the non-co-located inertia is given by:

$$\frac{\theta_2}{T_i} = \frac{d s + k}{J_1 J_2 s^4 + (J_1 + J_2) d s^3 + (J_1 + J_2) ks^2}$$

## Identification theory
Since every PATO setup is different and exact terms for the damping as well as the stiffness are not exactly known (and change over time), system identification will be performed via the direct method. The reason for this method over the 2 and 3 point method is because the open-loop system is stable. A diagram of how the measurements have been done is shown below.

![direct_method](/assets/images/pato-control/direct_method.png)

Where $h(t)$ is the to be identified plant and $v(t)$ is the noise which is always present, $u(t)$ is the input to the plant and $y(t)$ is the output of the plant. After Fourier transforming the signals we end up with:

$$ Y(f) = V(f) + H(f)U(f)$$
After multiplying both sides with the complex conjugate of the input from the right we get the following equation.

$$ Y(f)U^*(f) = V(f)U^*(f) + H(f)U(f)U^*(f)$$
Where $Y(f)U^*(f)$ is the [cross power spectral density](https://en.wikipedia.org/wiki/Spectral_density) (CPSD) between input and output and $V(f)U^*(f)$ is the CPSD of the noise and the input and $U(f)U^*(f)$ is the [auto power spectral density](https://en.wikipedia.org/wiki/Spectral_density) (APSD) of the input. The APSD is known and the CPSD of the output and input can also be calculated. However, to determine $H(f)$, the CPSD of the input and the noise should be as small as possible to get an estimate of the plant. In other words, the signals must not be correlated, for that reason white noise in the input is used. This way, the CPSD of the input and noise approach zero for sufficiently long measurement time 

## Design choices
### White noise
A variance of the [white noise](https://en.wikipedia.org/wiki/White_noise) can be altered such that the system shows the flexible modes that are of interest due to the spring. However, a too large variance can cause damage to the setup, so lower values are trialed first. After trial and error the white noise variance of 0.5 has been used. 
### Samples, measuring time
A total of 100 seconds are used to measure input data towards the setup and measuring both $\theta_1$ and $\theta_2$. Since the sample frequency is $4 \; [kHz]$, a total of 400.000 samples have been obtained per signal. 
### Window length, window type and overlap
The number of desired frames to average over is set to be 10 with a [Hanning window](https://en.wikipedia.org/wiki/Hann_function) and 50% overlap between frames. The window length is therefore 40.000 and the frequency resolution is therefore $0.1 \; [Hz]$ and [Nyquist frequency](https://en.wikipedia.org/wiki/Nyquist_frequency) is $2000 \; [Hz]$. The lowest frequency behavior that will be captured is $0.1 \; [Hz]$, for high performance motion systems this is sufficient, but for slow dynamics (Heat/chemical processes) the frequency range of interest might be lower, in those cases use less frames or longer measurement time. 

## Results

The system identification results for the co-located inertia is given by:

![motor_frf](/assets/images/pato-control/motor_frf.png)

When comparing this FRF with first principle modeling, the general form will be the same. But there are some interesting alterations present in this bode plot. The second resonance at high frequency is caused by the actual motor shaft which also has a stiffness that has a resonance at around $1000\; [Hz]$. The phase seems to be jumping around a lot, but this is simply caused by [phase wrapping](https://en.wikipedia.org/wiki/Instantaneous_phase_and_frequency#/media/File:Phase_vs_Time,_wrapped_and_unwrapped.jpg) since +180 and -180 are equal. The seemingly curved drop in phase is due to time delays in the system, and since this plot is on a logarithmic scale this drop in phase is linear.

![motor_coherence](/assets/images/pato-control/motor_coherence.png)

The [coherence](https://en.wikipedia.org/wiki/Coherence_(signal_processing)) is calculated by $C(f) = |H(f)|^2 \frac{S_{uu}(f)}{S_{yy}(f)}$ where the auto power spectral density of the input is the only variable which can be altered. For this reason the coherence at an anti-resonance are impossible to get to 1, since $H(f_{ar})=0$.

The system identification results for the non-co-located inertia is given by:

![load_frf](/assets/images/pato-control/load_frf.png)

The [bode-gain phase relation](https://en.wikipedia.org/wiki/Kramers%E2%80%93Kronig_relations#Magnitude_(gain)%E2%80%93phase_relation) does not hold for this frequency response, since a phase of -180 is expected for a -2 slope. In this case it is 0, where the reason could be that the encoder has its connection flipped around. For this reason control on the non-co-located side must be multiplied by -1. 

![load_coherence](/assets/images/pato-control/load_coherence.png)

From this point onwards, control on the non-co-located inertia is considered, since the goal is specified on the non-co-located inertia.

# Weak stabilizing lead/lag controller
To be able to tune a feedforward controller, the reference signal as well as the feedback controller need to be properly tuned with respect to each other such that a sufficient error trajectory can be seen. This is done by using a second order motion profile and a [lead filter](https://en.wikipedia.org/wiki/Lead%E2%80%93lag_compensator) to increase the phase of the open loop such that the bandwidth will be higher. The open loop together with the lead filter is shown below. 

![load_coherence](/assets/images/pato-control/weak_lead_lag.png)

This controller will yield the following specifications:

| Bandwidth $[Hz]$         | Modulus margin $[dB]$         | Phase margin [$^{\circ}$]        | Gain margin $[dB]$         |
|------------------|------------------|-----------------|-----------------|
|$ 5.58$     | $5.75$    | $50.0$     | $22.2$  |

Before implementing this controller in practice, we must check closed loop stability with the [Nyquist criterion](https://en.wikipedia.org/wiki/Nyquist_stability_criterion).

![nyquist_pd](/assets/images/pato-control/nyquist_pd.png)

There are no encirclements of the -1 point nor are there open-loop unstable poles, meaning that the closed-loop system is stable.

# Feedforward control 
The benefit of using [feedforward control](https://en.wikipedia.org/wiki/Feed_forward_(control)) in motion systems is that most of the time the reference is known, and it would be a waste of precious time to wait for the feedback controller to catch up. To be able to compensate for the reference beforehand we must tune three parameters $K_{fa}$ that is associated with the mass of the fixed body. $K_{fv}$ that accounts for the viscous friction and $K_{fc}$ that accounts for the coulomb friction. These parameters stay relatively constant after tuning, therefore feedforward can be tuned with any combination of feedback and reference. The parameters are in the order  $K_{fc} \to K_{fv} \to K_{fa}$. 

| $K_{fc}$        | $K_{fv}$        | $K_{fa}$|
|------------------|------------------|-----------------|
| $-8.0\cdot 10^{-3}$       | $-8.7\cdot 10^{-5}$        | $-3.9\cdot 10^{-4}$ |

The error trajectory after tuning feedforward is shown below.
![error_ff](/assets/images/pato-control/error_after_ff.png)
The purple lines represent the off-set during the turn around move. There is a constant error since the system has not been able to overcome the effect of friction. Meaning that feedforward tuning has been performed sub-optimally.

# Feedback control
First of all an inverse [notch filter](https://nl.mathworks.com/help/control/ug/discretizing-a-notch-filter.html) will be used at exactly $35 \; [rad/s]$ such that the tracking performance around this frequency will become as high as possible. This together with an overall higher bandwidth will prevent leakage of error from the turn around into the constant velocity phase. In order to increase the bandwidth, another notch filter must be implemented at exactly the resonance frequency such that the Nyquist contour wont encircle the -1 point. These notches together with a lead filter and a gain represent controller 1 and can be seen below.

![controller1](/assets/images/pato-control/controller_1.png)

The discrete controller calculated via [Tustin with pre-warping](https://en.wikipedia.org/wiki/Bilinear_transform) is also identified, such that we can make sure that the designed controller in the continuous domain matches the controller in the discrete domain.

To check the stability of the closed-loop the Nyquist contour must be checked for encirclements of the -1 point. 

![controller1_nyq](/assets/images/pato-control/nyquist_controller1.png)

The Nyquist contour does not have encirclements meaning that the closed-loop is stable.

Another controller (controller 2) is also tested, it contains the same components as controller 1, but it has an added integrator. Both the frequency content as well as the Nyquist contour can be seen below.

![controller2](/assets/images/pato-control/controller_2.png)

![controller2_nyq](/assets/images/pato-control/nyquist_controller2.png)

## Controller tuning parameters

### Controller 1

|Gain|Lead filter zero,pole|Inverse notch zero,pole|Notch zero,pole| 
|------------------|------------------|------------------|------------------|
| $-0.2$ | $1\; [Hz]$, $100\; [Hz]$| $5.57\; [Hz]\; \beta_{z,p} = \{0.5,0.01\}$   |$57.4 \;[Hz]\; \beta_{z,p} = \{0.01,0.5\}$ |

### Controller 2

|Gain|Lead filter zero,pole|Inverse notch zero,pole|Notch zero,pole|Integrator zero| 
|------------------|------------------|------------------|------------------|------------------|
| $-0.2$ | $1\; [Hz]$, $100\; [Hz]$| $5.57 \;[Hz]\; \beta_{z,p} = \{0.5,0.01\}$   |$57.4\; [Hz]\; \beta_{z,p} = \{0.01,0.5\}$ |$1\; [Hz]$ |

## Robustness

### Controller 1

| Bandwidth $[Hz]$         | Modulus margin $[dB]$         | Phase margin [$^{\circ}$]        | Gain margin $[dB]$         |
|------------------|------------------|-----------------|-----------------|
|$ 14.31$     | $5.53$    | $34.8$     | $8.4$  |

### Controller 2

| Bandwidth $[Hz]$         | Modulus margin $[dB]$         | Phase margin [$^{\circ}$]        | Gain margin $[dB]$         |
|------------------|------------------|-----------------|-----------------|
|$ 14.32$     | $6.40$    | $30.6$     | $8.4$  |

# Error performance
To have a metric on performance the power spectral density of the error during constant velocity is shown. The difference between controller 1 with feedforward, controller 2 with and without feedforward have been tested and plotted on top each other which is shown in the following figure.

![PSD_error](/assets/images/pato-control/PSD_error.png)

The RMS value of error for every implemented controller are as follows:

| Controller 1 with FF $[rad]$| Controller 2  $[rad]$    | Controller 2 with FF $[rad]$|
|------------------|------------------|-----------------|
| $0.0080 \; RMS$       | $0.025 \; RMS$      | $0.0022 \;RMS$ |


# Conclusion and discussion
## Conclusion
It is evident that the total error during constant velocity drastically decreases while using feedforward control. Furthermore, to reduce constant offsets during the constant velocity phase an integrator can be used inside the feedback controller. The requirement of a total error during constant velocity phase may be met on average, since the requirement was $0.006 \;[rad] $ whereas the measured error is $0.0022\; [rad] \; RMS$. But if this were for example a printhead, small peaks that go beyond $0.006 \; [rad]$ error  may be troublesome. In those cases, a low-pass filter should be used to suppress high frequency noise.
## Discussion

### Turn around move
The turnaround move can be tuned to be much faster if the maximum allowable acceleration and jerk is increased. Since this results in a much more aggressive reference signal, higher overshoot in error will occur at the turn around due to the torsion spring. This overshoot at the turn around can cause error during the constant velocity phase if the controller is not fast enough, therefore the controller bandwidth needs to increase. The fastest turnaround move can be found my maximizing the allowable controller effort $\pm 2.5 \;[V]$ while staying in the constraint of 0.006 $[rad/s]$ during constant velocity and staying closed-loop stable.
### Performance increase with feedback controller

The performance and robustness of the controller can be improved by first making a more accurate FRF measurement. This can be achieved by using a band-pass filter on the white noise, which causes the total energy injected into the system to decrease. The benefit is that the variance then can be increased to have a higher excitation of the frequencies of interest while keeping roughly the same energy injection. The coherence of the FRF measurement will be slightly higher for a larger frequency interval which means that the feedback controller can be pushed to the limits of the system. Once the FRF measurement is of higher confidence and the turn around move will become more aggressive, a band-pass filter on the controller itself is ought to be necessary to attenuate higher frequencies which will reduce noise in measurement signals.

### Performance increase of feedforward controller

To tune feedforward properly the feedback controller has to be weak and the reference has to be challenging enough such that derivatives of the reference signals are visible in the error. In this paper the acceleration blocks are not clearly visible, which implies that either the controller is too weak or the reference signal to challenging. It is recommended to keep either the controller of reference fixed and tune the other such that the acceleration blocks are clearly visible. A notch filter at exactly $5.5704 \; [Hz]$ can also be implemented before feedforward tuning to counteract the oscillations in the error signals caused by misalignment of the axles which allows for a more accurate parameter tuning.\\


### Future maintenance of controller
The final controller has a notch at exactly $57.6 [Hz]$ due to the resonance that depends on the spring constant. This spring constant changes with temperature and time which means this notch filter is not placed properly anymore which causes lack in performance and at some point even instability. Recommended is to keep track of data during normal operation and check for settling times and overshoot of the error. If drastic changes occur, re-tune controller with new FRF or replace the axle.

# Resources
## Books
[1] Franklin, G. F., Powell, J. D., &amp; Emami-Naeini, A. (2020). Feedback control of Dynamic Systems. Pearson. 
## Websites

[Third order motion profile analytical calculation](https://www.jpe-innovations.com/precision-point/third-order-point-to-point-motion-profile/)

[Loop Shaping tool 'Shapeit'](http://cstwiki.wtb.tue.nl/index.php?title=Home_of_ShapeIt)

[Discrete time controller implementation tool 'DCtools'](http://cstwiki.wtb.tue.nl/index.php?title=File:Dctools.zip)

## Course material
4CM00 ~ Control Engineering