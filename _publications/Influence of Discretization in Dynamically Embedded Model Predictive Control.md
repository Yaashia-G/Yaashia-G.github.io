---
layout: page
title: Influence of Discretization in Dynamically Embedded Model Predictive Control
description: A counter intuitive effect of discretisation time step in Dynamically Embedded MPC
img: assets/img/system.jpg
importance: 3
category: work
related_publications: true
math: true
---
_Access the paper  <a href="https://www.sciencedirect.com/science/article/pii/S2405896323016956" target="_blank">here </a>. Accepted to IFAC  World Congress 2023_

As is popularly known, Model Predictive Control is a popular control scheme for many complex systems. However, implementing it to continuous systems, $$ \dot x = f(x,u)$$ is almost impossible if we choose to solve a Continuous Time Optimal Control Problem (CT-OCP). So, a more practical method is to solve a Discretised Optimal Control Problem (DT-OCP). One of the ways to find the optimal control law is to solve the DT-OCP using a continuous solver. The result, hereafter
referred to as Dynamically Embedded MPC (DE-MPC), is
a continuous-time MPC scheme that tracks the solution to
the OCP with a bounded error, (cite). In this paper, we analyse the effect of discretisation on DE-MPC. Intuitively having more acccurate discretisation (smaller $$t_d$$) would lead to more stable control law, however that is not true.  

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
- MPC1: ts = td = 0.02s;
- HMPC: ts = 0.02s and td = 0.4s;
- MPC2: ts = td = 0.4s.
 
<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/error_di_ts0.02_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/t_di_err_ts0.02_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
   Left: Comparison of the position, velocity, and input trajectories for the three MPC schemes subject to an additive disturbance on the input. Right: The computation time for the three schemes with noise, compared to the sampling time ts = 0.02. HMPC is the only scheme that consistently satisfies the real-time requirements.
</div>
The disadvantage of MPC1 becomes apparent by examining the computation time required to solve the underlying
OCP. As shown, the time required to solve
MPC1 is approximately 30 times more than HMPC and
MPC2. This is because MPC1 has a prediction length of
N = 100 steps, whereas HMPC and MPC2 have a prediction
length of only N = 5 steps.

### Nonlinear Lane Change 
To emphasize the advantages of HMPC with a more
complex example, we consider the nonlinear lane change
system detailed in {% cite lane_change%}.
We compare the state and input trajectories obtained using three different schemes:
- MPC1: ts = td = 0.04s;
- HMPC: ts = 0.04s and td = 0.2s;
- MPC2: ts = td = 0.2s.
<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/x_nlc_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/tcomp_page-0001.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: The state trajectories for the nonlinear lane change model. The
trajectories of MPC1 and HMPC perform similarly, whereas MPC2 leads
to an unstable closed-loop response. Right: Computation time utilized by MPC1 and HMPC compared to the
sampling time ts. The computation time of MPC2 is not included due to
the fact that the response is unstable.
</div>







