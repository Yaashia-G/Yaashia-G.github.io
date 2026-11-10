---
layout: page
title: "CERG: Simulation Results"
description: Contact-friendly reference governor, validated in simulation. RSS 2025 HRCM Workshop
img: assets/img/FR3-gif.gif
importance: 3
category: published
related_publications: true
math: true
---

_Presented at the Workshop on Human-Robot Contact and Manipulation (HRCM), Robotics: Science and Systems (RSS) 2025. Read the abstract <a href="https://hrcm-workshop.github.io/2025/abstracts/gautam_abstract11.pdf" target="_blank">here</a>. The full paper, with hardware experiments, is on the [IFAC 2026 page]({{ '/publications/CERG/' | relative_url }})._

_**Y. Gautam**, N. Nechyporenko, C.-H. Lin, A. Roncone, M. M. Nicotra. "Compliant Explicit Reference Governor for Contact."_

## Background: the Explicit Reference Governor

The Explicit Reference Governor (ERG) is an optimization-free way to enforce constraints on complex systems such as robots {% cite ERG %}, {% cite MERCKAERT2022102223 %}. Instead of changing the controller, it changes the reference. Given a desired reference $$r$$, the ERG produces an auxiliary reference $$v$$ such that the system $$\dot x = f(x,u)$$ satisfies its constraints at all times, while $$\lim_{t \to \infty} v(t) = r$$ whenever $$r$$ is admissible. The auxiliary reference evolves according to a navigation field $$\rho(v,r)$$ scaled by a dynamic safety margin $$\Delta(v,x)$$:

$$ \dot v = \rho (v,r)\, \Delta (v, x)$$

and is applied to a prestabilized system, for example

$$ u = -K_P(x-v)-K_D\dot x.$$

The ERG is computationally cheap {% cite RRT+ERG %}, but it treats every constraint the same way: as something that must never be violated. That rules out contact entirely, which is a problem for tasks like wiping, pushing, or clearing a cluttered shelf.

## The Compliant ERG

The Compliant Explicit Reference Governor (CERG) distinguishes between two kinds of constraints:

- **Hard constraints** can never be violated: joint limits, velocity and torque limits, actuator saturation, forbidden surfaces.
- **Soft constraints** may be violated, meaning the robot may touch them, as long as the interaction is safe.

Safety during contact is defined through a **maximum energy** $$E_{\max}$$, which for simple systems maps directly to a maximum contact force. CERG guarantees that soft constraints are violated only when necessary and only within the energy bound, while hard constraints are never violated. In free motion, the energy bound is inactive, so the robot is not slowed down when nothing is in the way.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/cerg block diag.jpg" title="CERG architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    CERG architecture. The desired reference passes through the CERG, which sends the auxiliary reference to the prestabilized system.
</div>

The structure is the same as the ERG, but both the navigation field and the dynamic safety margin are split into soft and hard components, $$\rho_s(v,r), \rho_h(v,r)$$ and $$\Delta_s(v,x), \Delta_h(v,x)$$. The soft repulsion term lets the auxiliary reference penetrate a soft constraint up to a depth set by the **penetration constant** $$\delta_s$$, which in turn sets how hard the robot pushes at steady state. The compliant dynamic safety margin is built so that either no constraint is violated or only soft constraints are violated within the energy bound.

## Two-Link Robot Manipulator

The two-link (RR) manipulator model follows {% cite ModernRobotics %}. We prestabilize it in the end-effector frame:

$$ u = -K_P J(q)^\top (f(q) - f(v)) - K_D\dot q + g(q), $$

with $$K_P = 16I_2$$ and $$K_D = 10I_2$$. The reference $$r$$ is placed beyond a soft wall. Controlling in end-effector space lets the end effector get as close to the reference as the wall allows while staying under $$E_{\max}$$. The paper also discusses the joint-space alternative, which is better suited to hierarchical architectures.

<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_cdc_paper_vid.gif" title="RR arm, task-space CERG" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/f_5_0.1_ee.jpg" title="Contact forces" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    RR arm under task-space CERG against a very compliant wall: arm motion, auxiliary reference and interaction force. The end effector reaches the closest point along the wall.
</div>

### Effect of the penetration constant

With a small penetration constant, $$\delta_s = 0.1F_{\max}/K_P$$, the reference barely enters the soft constraint. The end effector slides along the wall and pushes gently.

<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_arm-gif.gif" title="Low penetration constant" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_arm force.jpg" title="Low penetration force" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    $$\delta_s = 0.1F_{\max}/K_P$$: low penetration, low contact force.
</div>

With a large penetration constant, $$\delta_s = 0.9F_{\max}/K_P$$, the end effector enters the soft constraint further and pushes harder, converging to a nonzero steady-state force.

<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_penetration-gif.gif" title="High penetration constant" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RR_penetration_F.jpg" title="High penetration force" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    $$\delta_s = 0.9F_{\max}/K_P$$: deeper penetration, higher contact force.
</div>

In both cases the contact force stays below $$F_{\max}$$, which is set by $$E_{\max}$$. The penetration constant tunes *how much* the robot pushes; the energy bound guarantees it never pushes too hard.

## Franka Emika in Drake

Finally, we test CERG on a 7-DoF Franka Emika in the <a href="https://drake.mit.edu/" target="_blank">Drake simulator</a> with a compliant point-contact model. The robot is subject to its real actuator, velocity and torque limits ([Franka specifications](https://frankaemika.github.io/docs/control_parameters.html)), and the reference sits inside the soft blue wall.

<div class="row">
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/FR3-gif.gif" title="Franka in Drake" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1745144434582-1dafd882-1eac-4e88-8d9d-4d506776544c_1.jpg" title="Contact forces with and without CERG" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    CERG on the Franka in Drake. The arm moves freely, makes contact and glides along the soft wall. Right: contact force with and without CERG.
</div>

Without CERG, the contact forces are significantly higher. With CERG, the robot moves at full speed in free space and keeps its energy below $$E_{\max}$$ once it is in contact, so it slides along the wall without damaging itself or the obstacle.

For what happens on a real robot, see the [IFAC 2026 page]({{ '/publications/CERG/' | relative_url }}).
