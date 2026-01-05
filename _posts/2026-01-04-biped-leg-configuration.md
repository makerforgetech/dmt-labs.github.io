---
layout: post
title: Bipedal Robot Leg Configuration
date: 2026-01-04 10:27
tags: [robotics, biped, design]
image: /assets/img/posts/2026-01-04-biped-leg-configuration/thumb.png
---

When designing a bipedal walking robot, one of the most important early design choices is how to configure the degrees of freedom in the legs. In the modular biped robot, each leg has four servos that control forward and backward motion as well as lifting and lowering the foot. The last servo could be configured in two ways:

- To rotate the toe inward and outward (a yaw joint), or

- To move the foot laterally under the body (a roll or abduction joint)

Both approaches are mechanically valid, but they affect the robot’s gait and balance in very different ways. This article explains the trade-offs between the two options, how they influence stability and control, and why I decided to prioritize lateral foot placement for this project.

## Two Possible Functions for the Final Leg Joint
### Option A: Toe Yaw

[![Biped Robot with Toe Yaw]( /assets/img/posts/2026-01-04-biped-leg-configuration/yaw.png)](/assets/img/posts/2026-01-04-biped-leg-configuration/yaw.png)

A toe yaw joint rotates the foot around the vertical axis. This can be useful for:

- Turning in place

- Following curved walking paths

- Reducing scuffing when the foot swings

- Creating more human-like movements

Toe yaw is common in advanced humanoid robots that focus on agility or expressive motion. However, it is not directly responsible for keeping the robot balanced or supporting weight during walking.

### Option B: Lateral Foot Placement

[![Biped Robot with Lateral Foot Placement]( /assets/img/posts/2026-01-04-biped-leg-configuration/lateral.png)](/assets/img/posts/2026-01-04-biped-leg-configuration/lateral.png)

A lateral foot placement joint allows the robot to shift the support foot sideways so that it sits more directly under the center of mass. This is important for:

- Standing on one leg without tipping

- Transferring weight from one leg to the other

- Quasi-static walking

- Adjusting stance width

- Recovering from small balance disturbances

This joint is closely related to center of mass and center of pressure control, which are fundamental to stable biped locomotion.

## Why Lateral Movement Matters More for Early Walking

Before a biped can walk reliably, it must be able to shift its weight over a single support leg. Without a lateral joint, this weight shift must be produced by leaning the torso or by ankle flexing, which is harder to control and less stable at low speeds.

With lateral foot placement, the robot can

- Move the stance foot under the body,

- Place the center of mass above it, and

- Lift the swing leg without tipping over.

This makes it possible to use a simple and stable quasi-static gait, which is well suited for an early walking prototype.

Toe yaw mainly improves maneuverability and turning behavior, which becomes useful only after stable walking and balance control are already established.

### Turning Without Toe Yaw

One practical question that came up during the design process was whether the robot would still be able to turn without a yaw joint. The answer is yes.

The robot can turn using step-based steering. Each step is placed slightly rotated or offset, and the body gradually follows the new direction as steps accumulate. This approach is slower than rotating in place, but it is much easier to stabilize during development and is widely used in many hobby and research bipeds.

## Engineering Trade-Off Summary

| Capability                        | Toe Yaw  | Lateral Foot Placement |
| --------------------------------- | -------- | ---------------------- |
| Turning in place                  | High     | Low                    |
| Curved walking                    | Moderate | Low                    |
| Single leg stability              | Low      | Very high              |
| Weight shifting                   | Low      | Very high              |
| Quasi-static walking              | Low      | Very high              |
| Balance robustness                | Low      | High                   |
| Importance for first walking gait | Low      | Critical               |

Toe yaw mainly improves maneuverability.
Lateral movement directly improves stability and balance.

For a robot that is still learning to walk, stability is the higher priority.


The main goal for this project is to achieve a stable, controllable walking gait before introducing more dynamic or complex behaviors. With that goal in mind, the lateral joint offers the greatest benefit:

- it simplifies weight transfer,

- it improves single leg support,

- it reduces reliance on torso leaning,

- it creates a more predictable control problem, and

- it makes tuning and experimentation less risky.

Toe yaw remains an interesting feature to add in a future revision, especially for tighter turning and smoother navigation. However, it is not the limiting factor at this stage of development. The limiting factor is balance, and lateral foot placement directly improves that.

# Conclusion

Choosing lateral foot placement instead of toe yaw was ultimately a decision that favored stability over maneuverability. It strengthens balance control, improves single leg support, and makes it possible to develop a robust first walking gait.

Toe yaw may be added later, but for now the priority is to build a robot that can walk confidently before teaching it how to walk elegantly.