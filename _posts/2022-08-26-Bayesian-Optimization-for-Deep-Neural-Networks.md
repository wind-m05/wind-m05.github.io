---
layout: article
title: "Bayesian Optimization for discrete hyperparameters in NARX model "
mathjax: true
tags: test
show_subscribe: false
---


## Summary
Still an ongoing project for discrete hyperparameter tuning of an Artificial Neural Network (ANN) with Bayesian Optimization (BO), where the number of nodes and number of input and output samples are being optimized for prediction and simulation error. 


### Why this project?
This project is purely for educational purposes and to learn how to use BO for discrete hyperparameters. The ANN will consist of 1 hidden layer and a varying amount of nodes to be optimized.

<!-- ## Personal objective
During my study I did the subject "Machine Learning for systems and control" which involved many different Machine Learning techniques like Gaussian processes, Artificial Neural networks, Reinforcement learning techniques like Q-learning and Actor-Critic based method all inside one large group project. During the group assignment I mainly focussed on Gaussian processes and Actor-critic reinforcement learning. For that reason I would like to continue learning more about the implementations of Deep Neural Networks and the benefits of using Bayesian optimization for hyperparameter tuning. -->

## Background
Using machine learning techniques date as far back as 1943, but implementations were often put aside due to lack of computational power. Nowadays, this computational power has been drastically increasing and therefore things are suddenly much more accessible than before. The idea with this project is to perform data-driven modeling of an actuated pendulum also called the "Unbalanced disk". More information about the setup and the datasets used you can check out [@GerbenBeintema](https://github.com/GerbenBeintema/gym-unbalanced-disk). The dynamics of the pendulum will be encapsulated by an [Artificial Neural Network](https://en.wikipedia.org/wiki/Artificial_neural_network) (ANN) that optimizes its neurons such that it can predict and simulate how the pendulum behaves. Designing such an ANN comes with a lot of guessing and hand tuning what the amount of hidden layers and the amount of neurons should be. This project is about trying to give an educated guess what the optimal amount of nodes are for an ANN with 1 hidden layer. [Bayesian Optimization](https://en.wikipedia.org/wiki/Bayesian_optimization#:~:text=Bayesian%20optimization%20is%20a%20sequential,expensive%2Dto%2Devaluate%20functions.) (BO) will be used to optimize the amount of nodes. This optimization technique uses [Gaussian Processes](https://en.wikipedia.org/wiki/Gaussian_process) (GP), because they are able to track uncertainties, which you need to be able to guess good query points based upon the exploration/exploitation trade-off. The benefit of using BO instead of for example [Grid Search](https://en.wikipedia.org/wiki/Hyperparameter_optimization) is that you need less trials to get to the same or even better result. The problem is that normal bayesian optimization relies on your hyperparameters to be continuous (which is normally a good thing), but for hyperparameters that are discrete in nature like the number of nodes or hidden layers of an ANN it is much more challenging.

## Goal 
Show that by using discrete BO on #nodes and #input and output samples yield better results within 1 hour of optimizing on a GTX 1080 GPU than using random search. This all will be done with a standard ANN with 1 hidden layer.


<!-- ### First principle modeling
$$
\begin{aligned}
\dot{\theta}(t) &=\omega(t) \\
\dot{\omega}(t) &=-\frac{M g l}{J} \sin (\theta(t))-\frac{1}{\tau} \omega(t)+\frac{K_{m}}{\tau} u(t)
\end{aligned}
$$ -->

### NARX model
A [Nonlinear Autoregressive Exogenous](https://en.wikipedia.org/wiki/Nonlinear_autoregressive_exogenous_model#:~:text=In%20time%20series%20modeling%2C%20a,of%20the%20same%20series%3B%20and) (NARX) model is used to describe the dynamics of the system. 



   
![NARX_model](/assets/images/bayes-opt-discrete/narx_jpg.jpg)


$$y_k = f(u_{k-1},u_{k-2},...,u_{k-n_b},y_{k-1},y_{k-2},...,y_{k-n_a}) + e_k$$

Where $y_k$ is the output of the model and $u_k$ is the input. The e_k term is the white-noise that influences $y_k$ and therefore also all its past values. The amount of past inputs that are taken into account are denoted by $n_b$ and the amount of past outputs that are taken into account to determine the current output are denoted by $n_a$. Both $n_a$ and $n_b$ are considered hyperparameters, because the amount of information needed to accurately describe the dynamics are both system and data dependent.  
### Artificial neural network structure

![NARX_model](/assets/images/bayes-opt-discrete/ann_struct.jpg)

### Prediction and simulation
Coming soon

# Implementation
Currently the different libraries, [bayes_opt](https://github.com/wind-m05/BayesianOptimization) , [botorch](https://botorch.org/) and [Ax](https://ax.dev/) are experimented with and checked for suitability for discrete hyperparameter tuning.

### Hyper parameters
Coming soon
### Acquisition function
Coming soon
### Kernel selection
Coming soon
### Activation function selection
Coming soon

