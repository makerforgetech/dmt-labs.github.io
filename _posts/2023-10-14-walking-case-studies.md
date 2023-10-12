---
title: Let's Learn About Walking Robots
date: 2023-10-14
categories: [Robotics, Modular Robotics Framework, Build Log]
tags: [robotics, walking, biped]     # TAG names should always be lowercase
image: /assets/img/posts/2023-10-14-walking-case-studies/thumbnail.png
---

The biggest question I get asked when showing my bipedal robot to people is is "Does it walk?". 

The answer is 'no', or rather, 'not yet'.

In order to understand the problem, we need to understand the basics of walking robots and the challenges that they face. This post will talk about a few examples available today.

_Image credit: [MathWorks](https://uk.mathworks.com/campaigns/offers/guide-to-understanding-reinforcement-learning-ebook.html)_

## A word on actuators

When we talk about actuators, we are talking about a components that move the robot. This could be a motor, a servo, hydraulics, a solenoid or even a muscle.

Generally there are three that are used in robotics for mobility:

1. **DC Motors** - These are the most common type of motor used in robotics. They are cheap, easy to control and can be found in a wide range of sizes and power ratings. They are also very easy to control using a microcontroller such as an Arduino or Raspberry Pi. The downside is that they are not very precise and can be difficult to control at low speeds. A variation on this is a BLDC (Brushless DC) motor which is more efficient and can be more precise, but requires a more complex control system.
1. **Servos** - Servos are a type of motor that includes a gearbox and a position sensor. This allows the motor to be controlled to a specific position and hold that position. Servos are very common in robotics and are used in many hobbyist projects. They are easy to control and can be very precise.
1. **Stepper Motors** - Stepper motors are a type of motor that can be controlled to move a specific number of steps (a degree of rotation). This also allows the motor to be controlled to a specific position and hold that position. Stepper motors are very common in robotics and are used in many hobbyist projects. They are typically quite bulky and require additional control circuitry.

## Boston Dynamics Spot and other quadrupeds

Boston Dynamics are a robotics company that have been working on walking robots for many years. They have a number of robots that are capable of walking, including the famous Spot robot. Although there are now many examples of quadruped robots, Spot is one of the most well known.

{% include embed/youtube.html id='qgHeCfMa39E' %}

Quadrupeds typically use DC motors combined with a gearbox to provide the power and precision required to move the legs. Gearboxes such as the planetary gear system are used to increase the torque of the motor and to reduce the speed. This allows the motor to be more precise and to move the legs at a slower speed.

![Planetary Gearbox Example](https://upload.wikimedia.org/wikipedia/commons/d/d5/Epicyclic_gear_ratios.png)
_Image credit: [Wikipedia](https://en.wikipedia.org/wiki/Epicyclic_gearing)_

The motors are typically mounted in the body of the robot and connected to the legs using a series of linkages. This allows the motors to be placed in a central location and the legs to be extended outwards.

![Quadruped Linkages](https://upload.wikimedia.org/wikipedia/commons/7/71/LegMechanism2DOF.gif){: .w-50}
_Image credit: [Wikipedia](https://en.wikipedia.org/wiki/Leg_mechanism)_

The quadruped robot is a great example of a robot that can become statically stable. This means that the robot can stand still without falling over. This is because the legs are spread out and the center of gravity is low. This is a great advantage for a robot that is designed to move around and interact with the world.

The current version of the modular biped is also statically stable, but in order to make it walk we will need to make it dynamically stable. This means that the robot will be able to balance itself while moving. This is a much more difficult problem to solve.

Let's take a look at some other examples.

## Soby - Bipedal wheeled robot

Soby has a very similar form factor to the modular biped. One of the key differences is it's mobility, which is possible because it has wheels instead of feet. 

{% include embed/youtube.html id='cunkAWZNXSU' %}

As you can see in the video, the wheels allow the robot to balance and move about the room. It is also able to move at a much faster speed than a walking robot.

The disadvantages to this approach is that, when not moving, the robot is still using power to balance itself. This is not a problem for a robot that is designed to move around, but for a robot that is designed to sit on a desk and interact with the world for the majority of it's time, this is not ideal.

We could look to be able to 'park' the robot, so that a resting position allows it to be dynamically stable, and then it can be 'activated' to move around the room. This may be something that we look at in a future variation, but for now we will focus on a walking robot.

## Dynamic locomotion (MIT)

This robot attempts to mimic the movement of a human. It uses a combination of DC motors and linkages to move the legs as I described above. It is able to balance dynamically, even with changes to the ground plane it is standing on.

{% include embed/youtube.html id='82v8it8OAK4' %}

The motors and gearboxes appear to be located solely in the 'hips', with belts to actuate the knee. This reduces the weight of the legs, allowing faster movement by reducing the weight of the legs.

Using BLDC motors is definitely an option, although the cost and complexity will reduce accessibility, which is something I am keen to avoid in this project.

## Birdbot - Biomimicry

This robot from Max Planck Institute for Intelligent Systems is a great example of biomimicry. It uses servos to actuate the legs combine with very clever system of springs and pulleys to mimic the movement of a bird. This allows for repeatable motion using fewer servos than with other walking robots.

{% include embed/youtube.html id='wwH40rYJt9g' %}

Again, the complexity to achieve this is high, which would reduce the accessibility of the project. It is also not clear how the mechanisms behave in a resting pose or when turning.

I would be very interest to see other systems using this approach in future.


## Parallel mechanisms

Parallel mechanisms are a type of mechanism that uses multiple actuators to move a single point. This is useful for robotics as it allows for a more compact design and can reduce the number of actuators required.

A good example of this can be seen below, from Disney Research.

{% include embed/youtube.html id='Kdto7qkE3A8' %}

This robot has great range of motion on each leg and is able to achieve a consistent walking gait.

## Simulations to train and test

There are a number of simulation tools available to help with the design and testing of walking robots. These allow faster iteration of the design and can help to reduce the cost of prototyping.

Simulations can also be combined with reinforcement learning to train the robot to walk in an environment that can be simulated and reset quickly. This is a great way to train the robot to walk without the risk of damaging the hardware, it also allows for thousands of simulations to be executed in the time that it would take to do a single test in the real world.

This robot shows this process in action on a small bipedal robot.

{% include embed/youtube.html id='Y0fBdpLf9ZI' %}

Disney Research also recently released footage of a robot that takes this approach to the next level. The robot was not only trained to walk with reinforcement learning, but was also trained to behave in certain way that appears animated.

{% include embed/youtube.html id='-cfIm06tcfA' %}

If you're interested in reinforcement learning, mathworks has a detailed e-book that covers the basic concepts: 

[MathWorks - Guide to Understanding Reinforcement Learning](https://uk.mathworks.com/campaigns/offers/guide-to-understanding-reinforcement-learning-ebook.html)

## What does it all mean?

With such a wide range of options available for mobility of the modular biped, it would make sense to utilise simulations to attempt to train the robot to walk.

Reinforcement learning is also an interesting avenue to investigate as it would allow the robot to learn to walk without the need for complex manual programming. This would also allow the robot to adapt to changes in the environment, such as a change in the ground plane or the addition of an obstacle.

I will likely investigate this approach in the future, so if you have suggestions for a simulation tool or reinforcement learning framework, or other examples of mobile biped robots, please let me know in the [community](https://github.com/dmt-labs/modular-biped/discussions).