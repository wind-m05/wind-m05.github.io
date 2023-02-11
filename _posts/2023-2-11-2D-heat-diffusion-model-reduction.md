---
layout: article
title: "Model reduction for the 2D heat diffusion equations in a plate using Galerkin projection and POD"
mathjax: true
tags: Model-reduction Galerkin-projection POD
show_subscribe: false
mathjax_autoNumber: false
cover: '/assets/images/model-reduction/model_reduction_cover'
---

This project focusses on describing the temperature distribution evolution of a 2D plate with as few equations as possible. In particular we will be interested in the heat diffusion process that takes place as a result of individual heat loads $u_1$ and $u_2$ applied on the surface. The equations will be approximated by using a Galerkin projection onto the heat diffusion equation. The fourier basis will be exploited to utilize its orthogonality properties. After which the POD basis will be defined from a high dimensional data set to represent the heat diffusion equations in only a few ordinary differential equations. 

## Heat diffusion equation
Firstly, the given heat diffusion equation is stated as follows.
$$
\rho(x, y) c(x, y) \frac{\partial T}{\partial t}(x, y, t)=\left[\begin{array}{ll}
\frac{\partial}{\partial x} & \frac{\partial}{\partial y}
\end{array}\right] K(x, y)\left[\begin{array}{l}
\frac{\partial T(x, y, t)}{\partial x} \\
\frac{\partial T(x, y , t)}{\partial y}
\end{array}\right]+u(x, y, t)
$$
Where,
- $T(x,y,t)$ ~ $[K]$ is the temperature in Kelvin.
- $\rho(x,y)$ ~ $[kg/m^3]$ is the density of the plate.
- $c(x,y)$ ~ $[J/(kg K)]$ is the heat capacity.
- $K(x,y)$ ~ $[W/(mK)]$ is the location dependent thermal conductivity matrix, which is positive definite.
- $u(x,y,t)$ ~ $[W/m^2]$ is the input heat flux into the plate.

For the rest of the project the plate is assumed to be isotropic and homogeneous.

## Fourier basis
The inner product will be defined as follows:
$$
    \left\langle \varphi_i,\varphi_j \right\rangle:=\int_0^{L_x}\int_0^{L_y}\varphi_i(x,y)\varphi_j(x,y)dydx.
$$

$\varphi_{k,l}$ represents the product of $\varphi_k(x)$ and $\varphi_l(y)$, which are defined as follows for non-negative integers $k$ and $l$ 

$$
\varphi_k(x) = 
    \begin{cases}
      \;\frac{1}{\sqrt{L_x}}\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\text{if}\;k=0\\
      \sqrt{\frac{2}{L_x}}\cos\left(\frac{k\pi x}{L_x}\right)\;\;\;\;\;\text{if}\;k>0
    \end{cases}
    \;\;\;\;\;\;\;\;\;\;
\varphi_l(y) = 
    \begin{cases}
      \;\frac{1}{\sqrt{L_y}}\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\text{if}\;l=0\\
      \sqrt{\frac{2}{L_y}}\cos\left(\frac{l\pi y}{L_y}\right)\;\;\;\;\;\text{if}\;l>0
    \end{cases} 
$$

## Galerkin projection 
We use the Galerkin projection to derive, for arbitrary $k$ and $l$, an explicit ordinary differential equation for the
coefficient function $a_{k,l}(t)$ in the spectral expansion given as:
$$
    T(x,y,t)=\sum_{k=0}^\infty\sum_{l=0}^\infty a_{k,l}(t)\varphi_{k,l}(x,y).
$$

The Galerkin projection of the homogeneous model given in \autoref{eq:2modelSimplified} is taken as follows
$$
    \left\langle \rho c \frac{\partial}{\partial t}T(x,y,t),\varphi_{k,l} \right\rangle=
    \left\langle \kappa \left(\frac{\partial^2}{\partial x^2}T(x,y,t)+\frac{\partial^2}{\partial y^2}T(x,y,t)\right) + u(x,y,t),\varphi_{k,l} \right\rangle
$$

Now, the spectral expansion given in \autoref{eq:spectralExpansion} is substituted into the Galerkin projection. However, the non-negative integers $k$ and $l$ of this spectral expansion are changed to $p$ and $q$ respectively, since $k$ and $l$ in \autoref{eq:spectralExpansion} are different from those in \autoref{eq:galerkin1}. Doing this results in 
$$
    \begin{aligned}
    &\left\langle \rho c \frac{\partial}{\partial t}\sum_{p=0}^\infty\sum_{q=0}^\infty a_{p,q}(t)\varphi_{p,q}(x,y),\varphi_{k,l}(x,y) \right\rangle=\\
    &\left\langle \kappa \left(\frac{\partial^2}{\partial x^2}\sum_{p=0}^\infty\sum_{q=0}^\infty a_{p,q}(t)\varphi_{p,q}(x,y)+\frac{\partial^2}{\partial y^2}\sum_{p=0}^\infty\sum_{q=0}^\infty a_{p,q}(t)\varphi_{p,q}(x,y)\right) + u(x,y,t),\varphi_{k,l}(x,y) \right\rangle.
    \end{aligned}
$$

Due to linearity of the inner product, the summations can be taken out of the inner product, moreover the $x$, $y$ and $t$ dependencies are dropped for ease of notation, this gives

$$
    \rho c \frac{\partial}{\partial t}\sum_{p=0}^\infty\sum_{q=0}^\infty a_{p,q}\left\langle \varphi_{p,q},\varphi_{k,l} \right\rangle=
    \kappa\sum_{p=0}^\infty\sum_{q=0}^\infty a_{p,q}\left\langle \frac{\partial^2}{\partial x^2}\varphi_{p,q}+\frac{\partial^2}{\partial y^2}\varphi_{p,q},\varphi_{k,l}\right\rangle + \left\langle u,\varphi_{k,l} \right\rangle.
$$

Since $\varphi_{p,q}$ is a product of the two basis functions given in \autoref{eq:phi}, the two double partial derivatives with respect to $x$ and $y$ position can be rewritten to $\ddot\varphi_p\varphi_q + \varphi_p \ddot\varphi_q$. Additionally, the partial derivative with respect to time of $a_{p,q}$ is rewritten as $\dot a_{p,q}$. The expression given in \autoref{eq:galerkin3} thus becomes

$$
    \rho c \sum_{p=0}^\infty\sum_{q=0}^\infty \dot a_{p,q} \left\langle \varphi_{p,q},\varphi_{k,l} \right\rangle=
    \kappa\sum_{p=0}^\infty\sum_{q=0}^\infty a_{p,q}\left\langle \ddot\varphi_p\varphi_q + \varphi_p \ddot\varphi_q,\varphi_{k,l}\right\rangle + \left\langle u,\varphi_{k,l} \right\rangle.
$$

Due to the orthonormality of $\varphi_{k,l}$ that was proven in Assignment 3, the summation on the left-hand side of the equation will only result in non-zero values when  $p$ and $q$ are equal to $k$ and $l$ respectively. This gives the following expression for $\dot a_{k,l}$

$$
    \dot a_{k,l} =\frac{\kappa}{\rho c}\sum_{p=0}^\infty\sum_{q=0}^\infty a_{p,q}\left\langle \ddot\varphi_p\varphi_q + \varphi_p \ddot\varphi_q,\varphi_{k,l}\right\rangle + \frac{1}{\rho c}\left\langle u,\varphi_{k,l} \right\rangle.
$$

Next up, evaluation of $\ddot\varphi_p\varphi_q + \varphi_p \ddot\varphi_q$ for different values of $p$ and $q$. This is done through \autoref{eq:phi}, keep in mind that $\ddot\varphi_p$ is the second partial derivative with respect to $x$ position and $\ddot\varphi_q$ is the second partial derivative with respect to $y$ position.

$$
    \begin{aligned}
    &\ddot\varphi_p\varphi_q + \varphi_p \ddot\varphi_q = 0\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \text{if}\;\;p = 0\;\;\text{and}\;\;q = 0\\
    &\ddot\varphi_p\varphi_q + \varphi_p \ddot\varphi_q = -\frac{p^2\pi^2}{L_x^2}\varphi_{p,q}\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \text{if}\;\;p > 0\;\;\text{and}\;\;q = 0\\
    &\ddot\varphi_p\varphi_q + \varphi_p \ddot\varphi_q = -\frac{q^2\pi^2}{L_y^2}\varphi_{p,q}\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \text{if}\;\;p = 0\;\;\text{and}\;\;q > 0\\
    &\ddot\varphi_p\varphi_q + \varphi_p \ddot\varphi_q = -\left(\frac{p^2\pi^2}{L_x^2}+\frac{q^2\pi^2}{L_y^2}\right)\varphi_{p,q}\;\;\;\;\;\;\;\;\;\; \text{if}\;\;p > 0\;\;\text{and}\;\;q > 0\\
    \end{aligned}
$$

By looking at the expressions above, it can be concluded that the fourth expression, describing the case in which both $p$ and $q$ are greater than zero, also holds for the cases when $p$ or $q$ are equal to zero. Thus \autoref{eq:galerkin5} can be written 

$$
    \dot a_{k,l} =-\frac{\kappa}{\rho c}\left(\frac{p^2\pi^2}{L_x^2}+\frac{q^2\pi^2}{L_y^2}\right)\sum_{p=0}^\infty\sum_{q=0}^\infty a_{p,q}\left\langle \varphi_{p,q},\varphi_{k,l}\right\rangle + \frac{1}{\rho c}\left\langle u,\varphi_{k,l} \right\rangle.
$$

Through the property of orthonormality of $\varphi_{k,l}$, this expression can again be rewritten 

$$
    \dot a_{k,l}=-\frac{\kappa}{\rho c}\left( \frac{k^2\pi^2}{L_x^2}+\frac{l^2\pi^2}{L_y^2}\right) a_{k,l}+ \frac{1}{\rho c}\left\langle u,\varphi_{k,l} \right\rangle.
$$

%Writing out the standard inner product of $u$ and $\varphi_{k,l}$, and 
Substituting back in the time and position dependencies gives

$$
    \dot a_{k,l}(t)=-\frac{\kappa}{\rho c}\left( \frac{k^2\pi^2}{L_x^2}+\frac{l^2\pi^2}{L_y^2}\right) a_{k,l}(t)+ \frac{1}{\rho c}\left\langle u(x,y,t),\varphi_{k,l}(x,y) \right\rangle.
$$

Therefore, a system of completely decoupled ordinary differential equations is found for the coefficient function $a_{k,l}(t)$.

## Initial conditions


## Simulation no input

<div>{%- include extensions/youtube.html id='jOofzffyDSA' -%}</div>

## Simulation with input 

<div>{%- include extensions/youtube.html id='jOofzffyDSA' -%}</div>

## POD basis

<div>{%- include extensions/youtube.html id='jOofzffyDSA' -%}</div>

## Comparison


## Conclusion

## Recommendations



![fourierblock](/assets/images/model-reduction/fourier_block.png)
![fourierinitial](/assets/images/model-reduction/fourier_initial.png)
![fourierinput](/assets/images/model-reduction/fourier_input.png)
![fouriernoinput](/assets/images/model-reduction/fourier_noinput.png)

![PODbasis](/assets/images/model-reduction/POD_basis_functions.png)
![POD](/assets/images/model-reduction/POD.png)
![PODdifferent](/assets/images/model-reduction/POD_different.png)




# Software
## Main code
~~~  matlab
%% 5CSA0 Tumor growth assignment
clc, clear all, close all

~~~
## Functions

### cell_interaction.m
~~~ matlab

~~~

### coupledfun.m
~~~ matlab

~~~

### d_cell_interaction.m
~~~ matlab

~~~

### d_history.m
~~~ matlab

~~~

### d_coupledfun.m
~~~ matlab

~~~

### stab_cell_interaction.m
~~~ matlab

~~~

### stab_coupledfun.m
~~~ matlab

~~~

# Resources
## Books
[1.] Khalil, H.K. (2014) Nonlinear systems. Upper Saddle River, NJ: Prentice Hall. 
## Websites
[Tutorial on delayed system simulation](http://matlab.imm.uran.ru/mirrors/www.cs.runet.edu/~thompson/webddes/tutorial.html)
## Course material
5LMA0 ~ Model Reduction