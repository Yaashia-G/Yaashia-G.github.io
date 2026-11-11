---
layout: page
title: "CERG: From Single Arms to Humanoids"
description: Energy bands that make contact safe, effective and adaptive. In preparation for T-RO
img: assets/img/cerg_ral_thumb.jpg
importance: 1
category: featured
related_publications: false
---

_In preparation for IEEE Transactions on Robotics (T-RO), submission planned for February 2027. Results on this page are preliminary._

_**Y. Gautam**, U. Yousaf, A. Roncone, M. M. Nicotra. "Compliant Explicit Reference Governors: Energy-Based Safety for Contact-Rich Manipulation from Single Arms to Humanoids."_

## The claim

**Specifying interaction energy by modulating the goal leads to improved safety, effectiveness and adaptability across controllers, tasks and robots.**

The [IFAC 2026 paper]({{ '/publications/CERG/' | relative_url }}) showed that an upper bound on interaction energy makes an existing controller safe for contact without slowing it down in free motion. This paper asks the next two questions:

| | Question |
|:--|:--|
| **Safe** | Can an energy bound make an existing controller safe for contact? ([IFAC 2026]({{ '/publications/CERG/' | relative_url }})) |
| **Effective** | Can the same safety layer also make sure the task gets done? |
| **Adaptive** | Can it adapt online to what each task needs? |

---

## Safe is not the same as done

In a collaboration with NASA and PickNik Robotics, we ran CERG on Phoebe, a dual-arm UR platform, on a mock-up of a space-station equipment panel.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/nasa_phoebe_imetro.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    CERG on Phoebe at PickNik Robotics, working on the iMetro panel mock-up.
</div>

Bounding the energy from above kept every contact safe. But on a spring-loaded box, safe was not enough: the arm never pushed too hard, and so it never pushed hard enough to keep the lid open.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/nasa_spring_box_fail.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Spring-loaded box with only an upper energy bound. The contact is safe, but the lid closes and the task fails.
</div>

---

## Effective: a floor on interaction energy

We add a lower energy bound alongside the upper one, so the robot is guaranteed to push at least hard enough, and never too hard.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/nasa_spring_box_emin.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    The same spring-loaded box with an energy floor added. The arm holds the lid open and completes the task.
</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/emin_sphere_push.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Franka pushing on a soft sphere with an increasing energy floor. The interaction energy (bottom plot) settles at the requested floor every time and never crosses the upper bound.
</div>

### Reaching the goal under model error

An unmodelled payload or contact force leaves the end effector short of its goal. We correct for this without touching the controller, so the safety guarantees still hold.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/no_integrator.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Without correction, the tip settles short of the goal on the sphere.
</div>

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/integrator_vs_none.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/integrator_payload.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Left: without and with correction, on the same surface goal. Right: with a 2 kg payload, the tip still reaches the true target.
</div>

### Two arms, one box of unknown mass

Two Franka arms lift a box whose mass they don't know. Each arm runs its own CERG and is unaware the other exists; the only coordination is through the box.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/dual_franka_box.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Dual-arm box pick on two Franka Emika robots. No shared controller and no box model; the box reaches the desired height.
</div>

---

## Adaptive: an energy band that changes with the task

A fixed energy band is chosen offline, per task. If the box is heavier than expected, the grasp slips. So we let the band move online: raise the floor to push more, lower the ceiling to push less, with safety maintained throughout.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include video.liquid path="assets/video/cerg-ral/band_change_contact.mp4" class="img-fluid rounded z-depth-1" controls=true %}
  </div>
</div>
<div class="caption">
    Changing the energy band while staying in contact with the sphere. The energy (top plot) tracks the band as it moves.
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
    Left: with a fixed band, the squeeze is not enough to hold the box and the pick fails. Right: with an adaptive band, the pick succeeds. Both at 2× speed.
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

_Stay tuned for the complete paper._
