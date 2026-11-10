---
layout: page
title: "DEFT: Fail-Active Trajectory Generation"
description: Diffusion policies that keep a damaged robot working. ICRA 2026
img: assets/img/deft_fig1.png
importance: 1
category: work
related_publications: true
math: true
---

_Published at the IEEE International Conference on Robotics and Automation (ICRA) 2026. Read the paper <a href="https://arxiv.org/abs/2602.02895" target="_blank">here</a>._

_G. G. Briscoe-Martinez, **Y. Gautam**, R. Shetty, A. Pasricha, M. M. Nicotra, A. Roncone. "Moving On, Even When You're Broken: Fail-Active Trajectory Generation via Diffusion Policies Conditioned on Embodiment and Task."_

## Motivation

When a robot joint fails today, the standard response is to freeze and wait for a human. That is safe, but it means downtime and a robot that cannot help itself. We want **fail-active** robots: robots that keep doing their job, fully or partially, even when damaged.

The difficulty is that every failure effectively turns the robot into a new robot. A joint that is locked, a joint with a reduced range, a joint that can only move slowly: each of these, and every combination of them across seven joints, is a different embodiment. The space of failures is combinatorial, so a planner that handles a handful of hand-picked failure cases does not scale.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/deft_fig1.png" title="DEFT overview" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    DEFT takes a structured encoding of joint-level failures and a task constraint, and generates a feasible trajectory with a diffusion model. Here the robot completes the task with a locked second joint, a slowed first joint and a reduced range on joint 5, falling back on a push-to-grasp strategy.
</div>

## Method

DEFT is a diffusion model that, given a start and goal joint configuration, generates joint positions and velocities over the full horizon. It is conditioned on two things:

- **Embodiment encoding** $$\xi \in \mathbb{R}^{4N}$$: the minimum and maximum position and velocity limit of every joint. Because $$\xi$$ is continuous, it describes not just *which* joints failed but *how badly*. Every velocity interval contains zero, so the robot always has a stable equilibrium.
- **Task constraint** $$\tau$$: a one-hot flag for constrained motion (planar, near-straight-line end-effector motion with fixed orientation, e.g. wiping or pushing a drawer) versus unconstrained motion (any feasible path, e.g. pick and place).

$$\xi$$ passes through a small MLP, is concatenated with $$\tau$$, and modulates a UNet denoiser through FiLM layers. Start and goal states are inpainted at every denoising step so the endpoints are exact, and the trajectory is clamped to the failure limits at every step, since conditioning alone guides the model but does not guarantee feasibility. Inference uses 25 denoising steps.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/deft_block_diagram.png" title="DEFT architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Overview of DEFT. Joint-level failures are encoded into a structured representation, combined with the task constraint, and used to condition the denoiser. Each step is clamped to the failure-induced joint limits.
</div>

**Training data.** Unconstrained trajectories come from RRT-Connect in joint space; constrained trajectories come from straight-line Cartesian paths solved with IK. All are smoothed with minimum-jerk optimization. Failures are sampled over 1 to 7 joints, with single-joint failures most likely, as reduced angle range, reduced velocity limit, or both.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/deft_primitives.png" title="Motion primitives" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The two motion primitives. Unconstrained: any feasible trajectory between two configurations. Constrained: planar, approximately straight-line end-effector motion with minimal orientation change.
</div>

## Simulation Results

We tested DEFT on a simulated 7-DoF Franka Emika Panda across **4.7k failure conditions** (about 2.9k angle failures, the rest velocity failures), each with 100 start-goal pairs: roughly **4.7 million trajectories**. 78% of the failure conditions were out of distribution with respect to training. The baselines are RRT for unconstrained motion and differential IK with optimization for constrained motion; note that each baseline handles only one primitive, while DEFT handles both.

| Question | Condition | Baseline | DEFT |
|:--|:--|:--:|:--:|
| Does it handle arbitrary failures? | Angle failure | 48.2% | **84.3%** |
| | Velocity failure | 32.5% | **70.8%** |
| Does it generalize to unseen failures? | In distribution | – | **78.3%** |
| | Out of distribution | – | **73.6%** |
| Does it handle multiple primitives? | Constrained | 30.9% | **46.4%** |
| | Unconstrained | 42.4% | **99.6%** |

Overall constraint satisfaction is **74.5% for DEFT versus 36.9% for the baseline**, a gain of 37.7 points (p < 10⁻¹⁰). Performance out of distribution is only a few points below in distribution, which is the point: DEFT is not memorizing failure cases.

## Real-World Experiments

We ran two multi-step tasks on the physical Franka with failures imposed on the hardware, 10 trials each, comparing DEFT against the optimization baseline and an ablation without embodiment conditioning (DEFT-NoConditioning).

- **Drawer:** open a drawer, push an object into a graspable pose, place it in the drawer, and close it. The robot alternates between unconstrained pick-and-place and constrained push/pull motions with joints 1–3 restricted.
- **Erasing:** grasp an eraser from the table and wipe a whiteboard, including a severe case with the elbow locked at $$q_4 = -2.4$$ rad.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/deft_real_tasks.png" title="Real-world tasks" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: erasing a whiteboard with an eraser grasped from the table. Right: opening a drawer, pushing an object into a graspable pose, placing it inside and closing the drawer.
</div>

| Method | Drawer | Erasing |
|:--|:--:|:--:|
| **DEFT** | **1.00 ± 0.00** | **1.00 ± 0.00** |
| Optimization baseline | 0.00 ± 0.00 | 0.35 ± 0.32 |
| DEFT-NoConditioning | 0.60 ± 0.49 | 0.93 ± 0.12 |

DEFT completed every trial of both tasks without violating joint or contact limits. The optimization baseline could not find a single feasible drawer plan and lost contact with the board on most erasing runs. Removing the embodiment conditioning hurt most on the drawer: jerky motions and surface-constraint violations caused the robot to drop the object. So while the ablation looked nearly as good in simulation, conditioning on the embodiment is what makes the difference on real hardware.

## Takeaways

DEFT adapts to joint failures without explicit replanning or switching policies, generalizes to failures it never saw in training, and handles both constrained and unconstrained motion in a single model. Next steps are real-time failure detection, transfer across robot platforms, and richer skills such as pivoting and throwing.
