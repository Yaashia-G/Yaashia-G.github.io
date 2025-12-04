---
layout: page
title: Compliant Explicit Reference Governor
description: ERG for contact friendly robotics
img: assets/img/cerg block diag.jpg
importance: 1
category: work
related_publications: true
math: true
---

_Access the preprint   <a href="https://doi.org/10.48550/arXiv.2504.09188" target="_blank">here </a>_

The Explicit Reference Governor (ERG)
is a control scheme that is an optimisation free alternative to
controlling complex systems, such as robots {% cite ERG %}, {% cite MERCKAERT2022102223 %} . It uses a reference governor that manipulates the reference, $$r$$, creating an auxillary reference such that the system, $$\dot x= f(x,u)$$ remains within the safety constraints at all given times. It is worth noting that $$\lim_{t \to \infty} v(t) = r$$. To achieve this, the ERG defines the gradient of the auxillary reference $$v$$, using a Navigation Field, $$\rho(v,r)$$ and a Dynamic Safety Margin, $$ \Delta(v,x) $$ as shown

$$ \dot v = \rho (v,r) \Delta (v, x)$$

This reference is then applied to the prestabilised system 

$$ u = -K_P(x-v)-K_D(\dot x)$$

While the ERG is a great scheme that is also computationally inexpensive {% cite RRT+ERG %}, it does not differentiate between constraints. We propose a scheme where we can differentiate between hard and soft constraints, giving more flexibility to our robotic systems. We can use this particular feature to be able to make contact friendly robots. We name this scheme Compliant Explicit Reference Governor (CERG). 


## Introduction
The CERG is a reference management scheme that differentiates between soft and hard constraints for contact friendly robotics. The hard constraints are non-violatable, for example: actuator saturation, torque limitations etc. However, the soft constraints are violatable and allow the robot to interact with the environment. 
One of the major concerns as the robot interacts with the environment is safety. We deal with this concern using Maximum Energy Limitations, which for simple systems can be converted into Maximum Force Limitations. The CERG algorithm is such that it ensures that soft constraints are only violated if necessary, and the interaction is safe, whereas hard constraints are never violated.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/cerg block diag.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Compliant ERG architecture. The reference is applied to the CERG system which then applies the auxillary reference to the prestabilised system. 
</div>

The CERG uses the same basic block diagram as the ERG, however the Compliant Navigation Field and Compliant Dynamic Safety Margin both have two components $$\rho_s(v,r), \rho_h(v,r)$$ and $$\Delta_s(v,x), \Delta_h(v,x)$$ to deal with soft and hard constraints respecitvely.  
In the paper (cite) we prove that the structure of the Compliant Dynamic Safety Margin is such that it ensures that either no constraints are violated, or if necessary, only soft constraints are violated within the energy limitations.  
## Experiments and Results
Numerical Experiments that validated the premise of the CERG scheme were run on a point mass system, a Two Link Robot Manipulator and on a simulation of the 7-DoF Franka Emika in Drake. All 3 levels of complexities have shown that the CERG performs as expected. The details of the point mass system are discussed in (cite)



### Two Link Robot Manipulator 
The details of the Two Link Robot can be found in {% cite ModernRobotics %}. For the purpose of this example, we choose our prestabilising law to be in end effector frame as shown 
$$ u = -K_P J(q)^\top (f(q) - f(v)) - K_D\dot q + g(q) $$
Where $$K_P = 16I_2, K_D = 10I_2$$. As seen in the figure below, the $$r$$ is beyond the soft constraint. Choosing the control law in end effector space allows the robot end effector to get as close to the constraint as possible, while still remaining under  $$E_{/max} $$. 
The discussion on choosing Task Space Controller as opposed to Joint Space Controller is present in the paper. The following figure shows the RR arm under Task Space Control with a very compliant wall
<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_cdc_paper_vid.gif" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/f_5_0.1_ee.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
 The motion of the arm, the auxiliary reference, as well as the forces of interaction. Notice how it is possible to get to the point closest along the wall using end effector control law.
</div>
Now to understand the effects of the penetration constant $$\delta_s$$, let us set it as $$\delta_s = 0.1F_{\max}/K_P$$.   
<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_arm-gif.gif" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_arm force.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    $$\delta_s = 0.1F_{\max}/K_P$$ A lower penetration constant means a relatively lower force of pushing. This also means that we don't 'enter' our soft constraint as much. 
</div>
As can be seen, the end effector trajectory of the RR arm slides along the soft wall. Notably, we don't violate the maximum energy of interaction. 

Now, if we set $$\delta_s = 0.9F_{\max}/K_P$$. As can be seen, the end effector trajectory enters the soft constraint. The force of pushing is also higher. At steady state we converge to $$F>0$$. 
<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_penetration-gif.gif" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_penetration_F.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
 $$\delta_s = 0.9F_{\max}/K_P$$ A higher penetration constant means a relatively higher force of pushing.  
</div>

It can be seen from these examples that the penetration constant effects how much we push against our soft constraints. In all cases interestingly, $$F <F_{\max}$$, which is set by the maximum energy of interaction $$E_{\max}$$, ensuring the safety of interaction.   

### Drake Simulations of the Franka Emika 
Lastly, we demonstrate the CERG on a more realistic simulation on the Franka Emika in <a href="https://drake.mit.edu/" target="_blank"> Drake simulator</a>. The contact model is chosen to be Compliant Point Contact. The Franka robot is subject to all the actuator, speed and torque limitations as specified [here](https://frankaemika.github.io/docs/control_parameters.html), the reference of the Franka is inside the soft blue wall.  
<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/FR3-gif.gif" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1745144434582-1dafd882-1eac-4e88-8d9d-4d506776544c_1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Compliant ERG validated on the Franka Emika robot in Drake. Forces of interaction with and without the CERG. 
</div>

## Hardware Experiments on the Franka Emika Panda

We validate the Compliant Explicit Reference Governor (CERG) on real hardware using the Franka Emika Panda robot across two representative manipulation tasks. The objective of these experiments is to assess how CERG enforces hard and soft constraints during contact-rich interaction, modulates the allowable reference motion under different energy bounds, and reduces impact forces during contact transitions.

To contextualize CERG’s performance, we compare it against two baselines:
- **Classical Explicit Reference Governor (ERG)**
- **Finely tuned Cartesian Impedance Controller**

The experiments are organized into two subsections below, each describing a task and showing video comparisons between CERG, ERG, and impedance control.

---

## 1. Wiping Task

In this task, the robot performs wiping motions over a flat surface. We vary the desired interaction energy bound \(E_{\max}\) and assess whether each controller can complete the task while respecting the imposed constraints.

CERG adapts the reference trajectory online to satisfy the energy bound \(E_{\max}\), producing smooth, stable contact while keeping joint and torque limits within bounds. In contrast, the baseline ERG cannot consistently enforce \(E_{\max}\): for tighter energy limits it either stalls or generates larger contact transients that violate the intended energy budget.

For comparison with the Cartesian impedance controller, we consider multiple initial conditions. When tuned to complete the task “perfectly” from a nominal start pose, the impedance controller often drives the robot into joint velocity limit violations or aggressive transients when the initial configuration is perturbed. CERG, in contrast, remains robust across these different initial conditions while still completing the wiping task.

### CERG Results
<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Cerg wiping.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>

### Baseline Controllers: ERG and Impedance
<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Erg wiping.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
    
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Impdenace jenga ideal.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>

<div class="caption">
Comparison of CERG, ERG, and impedance performance on the wiping task, highlighting energy-bound enforcement and robustness to different initial conditions.
</div>

---

## 2. Jenga Tower Pushing and Perturbation

In this experiment, the robot interacts with a Jenga tower through controlled lateral pushes and small perturbations. The goal is to inject just enough energy to explore the tower configuration without causing collapse.

CERG maintains a low-energy interaction regime by regulating the reference motion according to the chosen \(E_{\max}\), preventing large impulsive forces while still allowing meaningful manipulation of the tower. The ERG and impedance baselines, by contrast, frequently produce higher-impact contacts that deform or topple the tower, especially when tuned aggressively for task completion.

### CERG Results
<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Cerg jenga.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>

### Baseline Controllers: ERG and Impedance
<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Erg jenga.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Impedance jenga ideal.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>

<div class="caption">
Comparison of CERG, ERG, and impedance control during the Jenga pushing and perturbation task, emphasizing energy-bounded interaction and tower stability.
</div>





