---
layout: page
title: Hypersampled Model Predictive Control
description: Distinction between sampling and discretization
img: assets/img/flow2_page-0001.jpg
importance: 1
category: work
related_publications: true
math: true
---
_Access the paper  <a href="https://arxiv.org/abs/2310.02623" target="_blank">here </a>. Accepted to ACC 2024_

Model Predictive Control (MPC) is a popular constrained
control strategy for nonlinear systems. The idea behind
MPC is to solve an Optimal Control Problem (OCP) at
every instant and apply the first step of the optimal control
sequence to the system. . Although MPC is widely used due
to its stability, feasibility, and robustness properties (cite) , its
widespread adoption is hindered by the fact that solving the
optimal control problem in real time can be challenging. So we analyse the Hypersampled Model Predictive Control (HMPC) scheme, which is more efficient.

## Introduction
Consider the continuous time system $$ \dot x = f(x,u)$$. The aim is to derive an optimal control law by solving the Optimal Control Problem (OCP), {% cite HMPC %} Equation 3. This can prove to be _problematic_, since this is an infinite dimensional problem. So, usual methods of finding a control law is to discretise the system. This
approach assumes that the dynamic model of the system
matches the prediction model of the OCP. 

An unfortunate
consequence is that, for a fixed prediction horizon, reducing
the sampling time inevitably leads to an increased number
of prediction steps. This has the combined negative effect of
increasing the numerical complexity of the OCP while also
decreasing the allocated time for solving it.



<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/flow2_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Hypersampled Model Predictive Control. The continuous system is sampled at timestep ts to generate xk , which is then used to compute the
optimal control law to the discretized MPC problem
</div>

The paper seeks to formalize the distinction between _discretization time_  $$t_d$$, i.e., the step size used to discretize the continuous time system, and _sampling time_  $$t_s$$, i.e., the time at which the controller is implemented.

## Experiments and Results
Numerical Experiments that validate the efficiency of the HMPC scheme were run on a point mass system and a Lane change problem. All levels of complexities have shown that the HMPC performs better than traditional MPC schemes. 

We note the following properties:
• Benefits of Decreasing ts: Reducing the sampling
time tends to improve the performance of the controller
by making it more reactive to external disturbances.
Generally speaking, one would like ts to be as small
as possible to mimic continuous-time behaviour.
• Limit for Decreasing ts: Due to real-time imple-
mentation requirements, ts is lower-bounded by the
computational time required to solve (8).
• Benefits of Increasing td: Given a fixed prediction
horizon T , a larger discretization time step td leads to
less prediction steps N . Since the computational com-
plexity of (8) scales with N , it is generally beneficial
for td to be as large as possible.
• Limit for Increasing td: Since larger values of td
increase the discrepancy between 
an upper bound on the maximal admissible error will
translate into an upper bound on td.




### Point Mass System 
The details of the Point mass system with disturbance can be found in {% cite HMPC %}. We compare the performance of two MPC schemes and a HMPC scheme.
- MPC1: ts = td = 0.02;
- HMPC: ts = 0.02 and td = 0.4;
- MPC2: ts = td = 0.4.
Where $$K_P = 16I_2, K_D = 10I_2$$. As seen in the figure below, the $$r$$ is beyond the soft constraint. Choosing the control law in end effector space allows the robot end effector to get as close to the constraint as possible, while still remaining under the $$F_{/max} = 5N$$. 
Now to understand the effects of the penetration constant $$\delta_s$$, let us set it as $$\delta_s = 0.1F_{\max}/K_P$$.   
<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/error_di_ts0.02_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/t_di_err_ts0.02_page-0001" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
   
</div>
As can be seen, the end effector trajectory of the RR arm slides along the soft wall. Notably, we don't violate the maximum force of interaction. 

### Nonlinear Lane Change 

<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/x_nlc_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/tcomp_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">

</div>

It can be seen from these examples that the penetration constant effects how much we push against our soft constraints. In all cases however, $$F <F_{\max}$$, which ensures the safety of interaction.   





