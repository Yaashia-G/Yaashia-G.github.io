---
layout: page
title: "CERG: From Single Arms to Humanoids"
description: Energy bands that make contact safe, effective and adaptive. In preparation for T-RO
img: assets/img/cerg_ral_thumb.jpg
importance: 6
category: in progress
related_publications: false
math: true
---

_In preparation for IEEE Transactions on Robotics (T-RO), submission planned for February 2027. Results on this page are preliminary._

_**Y. Gautam**, U. Yousaf, A. Roncone, M. M. Nicotra. "Compliant Explicit Reference Governors: Energy-Based Safety for Contact-Rich Manipulation from Single Arms to Humanoids."_

## The claim

**Specifying interaction energy by modulating the goal leads to improved safety, effectiveness and adaptability across controllers, tasks and robots.**

The [IFAC 2026 paper]({{ '/publications/CERG/' | relative_url }}) answered the first question: an upper energy bound $$E_{\max}$$ makes an existing controller safe for contact without slowing it down in free motion. This paper takes on the next two:

| | Question | Mechanism |
|:--|:--|:--|
| **Safe** | Can an energy bound make an existing controller safe for contact without slowing it down? | $$E_{\max}$$ ([IFAC 2026]({{ '/publications/CERG/' | relative_url }})) |
| **Effective** | Can the same layer make sure the task actually gets done? | $$E_{\min}$$ + reference integrator |
| **Adaptive** | Can the energy band adapt online to what the task needs? | Adaptive band $$[E_{\min}, E_{\max}]$$ |

The starting observation: **safe is not the same as done.** With only $$E_{\max}$$, CERG guarantees the robot never pushes too hard, but nothing guarantees it pushes *hard enough*. A spring-loaded lid stays open, a heavy box slips out of a grasp. Safety alone leaves tasks unfinished.

---

## Effective: a minimum energy and a reference integrator

### A floor on interaction energy

We add a lower bound $$E_{\min}$$ alongside $$E_{\max}$$. When the interaction energy drops below $$E_{\min}$$, the soft-constraint penetration $$\delta_s$$ grows: the applied reference sinks further into the surface and the PD spring presses harder, until the energy is back inside the band. $$E_{\max}$$ still holds at all times; $$E_{\min}$$ is satisfied at steady state.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/emin_sphere_push.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Franka pushing on a soft sphere with increasing $$E_{\min}$$. The closed-loop energy (bottom plot) settles at the requested floor in each run, and never crosses $$E_{\max}$$.
</div>

### Reaching the goal: integrate the reference, not the input

A PD+gravity controller with an inverse-kinematics reference has steady-state error whenever the model is off: an unmodelled payload, a contact force, an IK approximation. Without correction, the end effector never quite reaches the goal on the surface.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/no_integrator.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    No integrator ($$K_i = 0$$): the tip settles short of the goal on the sphere.
</div>

The usual fix, integral action on the controller input, would break the guarantees CERG relies on, because the prestabilized system is no longer the one the dynamic safety margin was designed for. Instead, we place the integrator **outside** the loop, on the task-space reference: the end-effector error is integrated and used to shift the reference fed to inverse kinematics, before CERG. CERG still sees the same prestabilized PD+G system, so constraint satisfaction and stability are preserved, and the integrator removes the steady-state error.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/integrator_vs_none.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/integrator_payload.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Left: without the integrator ($$K_i = 0$$) versus with it ($$K_i = 0.7$$), on the same surface goal. Right: with a 2 kg payload, the integrator shifts the goal until the tip reaches the true target.
</div>

### On hardware: two arms, one box of unknown mass

Two Franka arms lift a box whose mass they don't know. Each arm runs its own CERG and is unaware the other exists; the only coordination is through the box. With $$E_{\min}$$ holding the squeeze and the integrator correcting for the unknown weight, the box reaches the desired height.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/dual_franka_box.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Dual-arm box pick on two Franka Emika robots. No shared controller, no box model: the energy floor and reference integrator do the work.
</div>

---

## Adaptive: an energy band that changes with the task

A fixed band has its own limit: it is chosen offline, per task. A heavier box than expected, and the grasp slips. So we let the band move online. There are two ways to adapt it:

- **Push more:** raise $$E_{\min}$$. Below the floor, $$\delta_s$$ grows and the robot presses harder. Satisfied at steady state.
- **Push less:** lower $$E_{\max}$$. Satisfied at all times.

A latch keeps $$E_{\max}$$ safe at every instant, even while $$E_{\min}$$ is being retuned in the middle of contact.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/band_change_contact.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Changing $$[E_{\min}, E_{\max}]$$ while staying in contact with the sphere. The energy (top plot) tracks the band as it moves.
</div>

### Humanoid box pick

The same layer on a simulated Unitree H1-2 humanoid lifting a box with both arms.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/h1_box_fail.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/h1_box_success.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Left: with a fixed band, the squeeze is not enough to hold the box and the pick fails. Right: with an adaptive energy band, the pick succeeds. (Both 2× speed.)
</div>

### Humanoid wiping: tile firmly, glass gently

One wiping task, two surfaces with opposite needs: tile needs firm pressure, glass needs a gentle touch. The energy band shifts between them while the robot stays in contact; the controller underneath stays the same.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/h1_wiping_tile_glass.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Energy-band wiping on the H1-2 in simulation. The interaction energy (top right) stays inside a firm band on tile and a gentle band on glass. 2.5× speed.
</div>

---

## Where this is going

Remaining work for the paper: baseline comparisons (unfiltered controller and standard ERG) on the same tasks and metrics, and dual-arm experiments in both simulation and hardware. One open limitation is that the band is still set by hand; choosing it automatically from the task is the natural next step.
