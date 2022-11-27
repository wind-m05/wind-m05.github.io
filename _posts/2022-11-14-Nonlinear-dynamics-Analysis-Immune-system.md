---
layout: article
title: "Nonlinear dynamics of the Immune system"
mathjax: true
tags: Non-linear Lyapunov-stability Bifurcations Delayed-systems
show_subscribe: false
---


# Summary

# Introduction
<!-- This project was part of the course 'Modeling dynamics' (5SCA0) which was the first modeling course that focussed on non-linear systems. The content of the course was mainly mathematical and therefore not specific towards any engineering field. Examples in mechanical/electrical and chemical engineering have been proposed by defining the dynamic behavior with use of [port-Hamiltonian models](https://www.math.rug.nl/arjan/DownloadPublicaties/ICMvanderSchaft.pdf). -->
Understanding the interaction between malignant cells and the immune system of an organism deserves quite some attention. A system theoretic perspective on this understanding proves quite useful for studying the dynamic features of tumor growth and to understand the implication on specific treatments. In fact, such a perspective brings valuable insight in some key mechanisms that are used in immunology, oncology and cancer biology. This will be the purpose of this project. The system of nonlinear coupled differential equations are given by:

$$
\begin{aligned}
&\dot{M}=1+a_1 M(1-M)-a_2 M H \\
&\dot{H}=a_3 H R-a_4 H \\
&\dot{R}=a_5 R(1-R)-a_6 H R-a_7 R
\end{aligned} 
$$

Where

- $M$ is the density of the malignant or tumor cells,
- $H$ is the density of the active hunting cells,
- $R$ is the density of the resting cells in the immune system.

All indicated parameters $a_i$ are non-negative numbers. In particular,

- $a_1$ is the growth rate of tumor cells,
- $a_2$ is the rate of destruction of tumor cells by the hunting cells,
- $a_3$ is the conversion rate from resting cells to hunting cells,
- $a_4$ is the natural death rate of hunting cells,
- $a_5$ is the growth rate of resting cells,
- $a_6$ is the conversion rate from hunting to resting cells,
- $a_7$ is the natural natural death rate of resting cells.

# Fixed points and biological feasibility
The fixed points can be determined by solving the equations of the states, because the fixed points in a system are the solutions of the set given in \autoref{eq:OGmodel} when there is no time dependency. Firstly, the rate of change of density of the hunting cells ($\dot{H}=0$) is solved. 

$$
\begin{aligned}
& a_3 H R-a_4 H=0 \\ 
& H(a_3 R-a_4)=0 \\ 
& H^*_1=0 \vee R^*_1=\frac{a_4}{a_3}
\end{aligned}
$$

$H^*_1$ will be substituted into \autoref{eq:OGmodel} such that $M^*_{1,2}$ can be computed by solving $\dot{M}=0$.

$$
\begin{aligned}
& 1+a_1M(1-M)-a_2 M H=0 \\ 
&H^*_1=0\\
& 1+a_1 M-a_1 M^2=0 \\ 
& M^2-M -\frac{1}{a_1}\\ 
& M^*_{1,2}=\frac{1}{2}\pm\sqrt{\frac{1}{4}+\frac{1}{a_1}} 
\end{aligned}
$$

Next, $H^*_1$ is substituted into \autoref{eq:OGmodel} where $\dot{R}$=0 to obtain  $R_2$ and $R_3$: 

$$
\begin{aligned}
& H^*_1=0\\
& a_5 R(1-R) - a_7 R=0 \\
& R(a_5-a_5 R -a_7)=0 \\
& R^*_2=0 \vee R^*_3=1-\frac{a_7}{a_5}
\end{aligned}
$$

$H^*_2$ can be computed by implementing $R^*_1$ in $\dot{R}$=0:

$$
\begin{aligned}
& R^*_1=\frac{a_4}{a_3}\\
& a_5 R(1-R)-a_6 H R-a_7 R=0 \\
& \frac{a_4}{a_3}\cdot(a_5-a_5\cdot \frac{a_4}{a_3}-a_6\cdot H -a_7)=0 \\
& H^*_2=\frac{a_5 a_3 -a_4 a_5 -a_3 a_7}{a_3 a_6}
\end{aligned}
$$

Finally, $H^*_2$ can be substituted in the equation $\dot{M}$=0 to obtain $M_{3,4}$:

$$
\begin{aligned}
& H^*_2=\frac{a_5 a_3 -a_4 a_5 -a_3 a_7}{a_3 a_6}\\
& 1+a_1 M(1-M)-a_2 M H_2=0 \\
& M^*_{3,4}=(\frac{1}{2}-\frac{a_2}{2a_1}H_2^*)\pm \sqrt{(\frac{a_2}{a_1}H_2-1)^2+\frac{1}{a_1}}
\end{aligned}
$$

The resulting fixed points are stated below:

$$
\begin{aligned}
& (M^{*}_1,H^{*}_1,R^{*}_2)=(\frac{1}{2}+\sqrt{\frac{1}{4}+\frac{1}{a_1}},0,0)\\
& (M^{*}_2,H^{*}_1,R^{*}_2)=(\frac{1}{2}-\sqrt{\frac{1}{4}+\frac{1}{a_1}},0,0)\\
& (M^{*}_1,H^{*}_1,R^{*}_3)=(\frac{1}{2}+\sqrt{\frac{1}{4}+\frac{1}{a_1}},0,1-\frac{a_7}{a_5})\\
& (M^{*}_2,H^{*}_1,R^{*}_3)=(\frac{1}{2}-\sqrt{\frac{1}{4}+\frac{1}{a_1}},0,1-\frac{a_7}{a_5})\\
& (M^*_3,H^*_2,R^*_1)=\frac{1}{2}(\frac{a_2}{a_1}H_2^*-1)+ \sqrt{\frac{1}{4}\cdot(\frac{a_2}{a_1}H_2^*-1)^2+\frac{1}{a_1}},\frac{a_5 a_3 -a_4 a_5 -a_3 a_7}{a_3 a_6},\frac{a_4}{a_3})\\
& (M^{*}_4,H^{*}_2,R^{*}_1)=\frac{1}{2}(\frac{a_2}{a_1}H_2^*-1)- \sqrt{\frac{1}{4}\cdot(\frac{a_2}{a_1}H_2-1)^2+\frac{1}{a_1}},\frac{a_5 a_3 -a_4 a_5 -a_3 a_7}{a_3 a_6},\frac{a_4}{a_3})\\
\end{aligned}
$$

The fixed points stated in the equations above need to be checked on feasibility. In other words, the values of the fixed points require to be in set $\mathcal{P}$ stated in \autoref{eq:P_set}:


- Since $R^*_2$ and $H^*_1$ are equal to zero, they are feasible.
- Because all $a_i$ must be positive, $R^*_1$ is also feasible.
- $H^*_2$ and $R^*_3$ are feasible due to the sufficient condition: $\frac{a_4}{a_3}+\frac{a_7}{a_5}<1$. The reasoning behind this condition being sufficient will be further explained in question 3.
- Parameter $a_1$ and variables $M^*_{1,2}$ must be positive to be in set $\mathcal{P}$. As a result, only $M^*_1$ is feasible because: $\frac{1}{2}<\sqrt{\frac{1}{4}+\frac{1}{a_1}}$ for any $a_1>0$. For this reason, $M^*_{2}=\frac{1}{2}-\sqrt{\frac{1}{4}+\frac{1}{a_1}}$ will be negative and hence not feasible. 
- Parameters $a_1,...,a_7$ and variables $M^*_{3,4}$ must be positive for $M^*_{3,4}$ to be feasible. To prove this, $\frac{a_2}{a_1}H_2^*-1$ is repaced by variable y.  $M^*_{3,4}=\frac{1}{2}y \pm \sqrt{\frac{1}{4}\cdot y^2+\frac{1}{a_1}}$. Here $\frac{1}{2}y < \sqrt{\frac{1}{4}\cdot y^2+\frac{1}{a_1}}$ for every $y\in \mathbb{R}$ and $a_1>0$. Hence, $M^*_{3}$ will be positive meaning it is feasible and $M^*_{4}$ negative meaning it is unfeasible.   


Taking into account all the results above, the following fixed points are in set $\mathcal{P}$: 

$$
\begin{align}
    & (M^{*}_1,H^{*}_1,R^{*}_2)=(\frac{1}{2}+\sqrt{\frac{1}{4}+\frac{1}{a_1}},0,0)\\
    & (M^{*}_1,H^{*}_1,R^{*}_3)=(\frac{1}{2}+\sqrt{\frac{1}{4}+\frac{1}{a_1}},0,1-\frac{a_7}{a_5})\\
    & (M^*_3,H^*_2,R^*_1)=\frac{1}{2}(\frac{a_2}{a_1}H_2^*-1)+ \sqrt{\frac{1}{4}\cdot(\frac{a_2}{a_1}H_2^*-1)^2+\frac{1}{a_1}},\frac{a_5 a_3 -a_4 a_5 -a_3 a_7}{a_3 a_6},\frac{a_4}{a_3})
\end{align}
$$













# Stability and contractive properties

# Time delayed system

# Stabilization

# Conclusion & Discussion
## Conclusion
## Discussion
# Resources
## Books
## Websites
## Course material
5CSA0 ~ Modeling Dynamics