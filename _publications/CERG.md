---
layout: page
title: "CERG: Hardware Experiments"
description: Energy-bounded contact on a real Franka. IFAC World Congress 2026
img: assets/img/cerg block diag.jpg
importance: 2
category: featured
related_publications: true
math: true
---

_Published at the IFAC World Congress 2026, Busan, South Korea. Read the paper <a href="https://doi.org/10.48550/arXiv.2504.09188" target="_blank">here</a>._

_**Y. Gautam**\*, G. B. Martinez\*, A. Mohan, N. Nechyporenko, A. Roncone, M. M. Nicotra. "Compliant Explicit Reference Governor for Contact-Friendly Robotic Manipulators." (\* equal contribution)_

## The idea in one paragraph

The Compliant Explicit Reference Governor (CERG) is a modular safety layer that sits between a high-level planner and a low-level controller. It never touches the controller; it only reshapes the reference the controller tracks. CERG enforces **hard constraints** (joint, velocity and torque limits, forbidden surfaces) at all times, and allows contact with **soft constraints** only while the robot's total energy stays below a bound $$E_{\max}$$. In free motion the energy bound is inactive, so safety costs nothing when there is nothing to hit. The theory and simulation results are on the [RSS 2025 page]({{ '/publications/CERG_RSS/' | relative_url }}).

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/cerg block diag.jpg" title="CERG architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    CERG architecture. The desired reference passes through the CERG, which sends an auxiliary reference to the prestabilized robot.
</div>

## How CERG enforces the energy bound

Contact is allowed when the robot is outside the soft constraint, $$s(q) < 0$$, *or* when its energy is below the bound. Writing $$V(x,v)$$ for the kinetic plus virtual potential energy of the prestabilized robot, the combined requirement is

$$ \min\big(s(q),\; V(x,v) - E_{\max}\big) \le 0. $$

This "either/or" is handled directly in the dynamic safety margin, with no disjunctive optimization:

$$ \Delta(x,v) = \min\Big(\Delta_h,\; \max\big(\Delta_s,\; \Delta_E\big)\Big), \qquad \Delta_E = \kappa_E\,\big(E_{\max} - V\big), $$

where $$\Delta_h$$ and $$\Delta_s$$ are the margins for the hard and soft constraints and $$\Delta_E$$ is the energy margin. The hard margin always wins. Between soft constraint and energy, the robot only needs one of the two to be safe.

## Hardware experiments

We ran CERG on a Franka Emika Panda on two contact-rich tasks and compared it against two baselines:

- **Explicit Reference Governor (ERG):** same architecture, but no notion of soft constraints or energy.
- **Cartesian impedance control:** the standard choice for contact, carefully hand-tuned.

| Task | Controller | Task completed | $$E_{\max}$$ respected | Hard constraints enforced |
|:--|:--|:--:|:--:|:--:|
| Wiping | Impedance | ✓ | ✗ | ✗ |
| | ERG | ✓ | ✗ | ✓ |
| | **CERG** | **✓** | **✓** | **✓** |
| Jenga pushing | Impedance | ✗ | ✗ | ✗ |
| | ERG | ✗ | ✗ | ✓ |
| | **CERG** | **✓** | **✓** | **✓** |

CERG is the only controller that completes both tasks while respecting both the energy bound and the hard limits.

---

## 1. Wiping a whiteboard

The robot wipes a whiteboard, a task that needs sustained contact with the right amount of pressure. We repeat it under three different energy bounds $$E_{\max}$$ and from different starting poses.

### CERG

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Cerg wiping.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    CERG wiping the whiteboard. The reference is reshaped online so the robot keeps contact with the board while its energy stays under E<sub>max</sub>, and the joint and velocity limits are never violated.
</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg_wiping_good.webm" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    CERG starting far from the whiteboard. The same parameters still complete the task: the governor slows the approach only as much as the hard limits require.
</div>

### Baselines

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Erg wiping.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/wiping_impedance_ideal.webm" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Left: ERG completes the wipe, but it has no energy term, so it cannot enforce E<sub>max</sub> during contact. Right: impedance control tuned to complete the task from the nominal start pose.
</div>

<div class="row mt-3">
  <div class="col-md-6 col-sm-12 mt-3 mt-md-0">
    {% include video.liquid path="assets/video/impedance_jenga_bad.webm" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    The same impedance gains from a start pose far from the board. The robot accelerates too hard, hits its joint velocity limit and stops. Gains tuned for one start pose do not transfer to another; CERG's do.
</div>

---

## 2. Pushing a Jenga tower

The robot pushes a Jenga tower across the table. This is a delicate task: too much energy at contact and the tower falls. The goal is to inject just enough energy to move it.

### CERG

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Cerg jenga.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    CERG pushes the tower without toppling it. The energy bound caps the impact at contact, and the push continues at a pace the tower can take.
</div>

### Baselines

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/Erg jenga.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/impedance_jenga_ideal.webm" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Left: ERG hits the tower with too much energy and topples it. Right: impedance control hand-tuned for this exact start pose.
</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/bad_impedance_jenga.webm" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/impedance_jenga.webm" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Impedance control from a farther start pose. Left: with stiff gains, the robot exceeds its joint velocity limit and stops. Right: with softer gains, it stays within its limits but cannot make a smooth transition into contact, and the tower falls.
</div>

---

## Takeaway

Impedance control can be tuned to work, but only for one start pose and one task. ERG enforces hard limits but has no way to reason about contact. CERG gets both: hard limits always, and contact energy bounded by a single interpretable number, $$E_{\max}$$, with no retuning across start poses.
