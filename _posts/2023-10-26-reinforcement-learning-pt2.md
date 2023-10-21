---
layout: post
title: "Teaching a robot to walk - Part Two"
date: 2323-10-26 
categories: [Robotics, Guides, Reinforcement Learning, Simulation]
tags: [robotics, walking, biped, reinforcement-learning]     # TAG names should always be lowercase
image: /assets/img/posts/2023-10-26-reinforcement-learning-pt2/thumbnail.png
---

Following on from my previous post, I wanted to be able to take the Google Colab notebook and run it on a custom model for the Modular Biped. 

The physics simulator model is [Mujoco](https://github.com/google-deepmind/mujoco/releases). Let's download and install.

## Download and install Mujoco

Download the latest version of Mujoco from [here](https://github.com/google-deepmind/mujoco/releases). 

For Ubuntu, you can download the latest release file `mujoco-X.X.X-linux-x86_64.tar.gz` from the link above, then simply unzip and, in your terminal, navigate to the directory's `bin` folder and run `./simulate`.

## Test a model

To test a model, simply drag and drop the models into the simulator while running. The model will be loaded automatically. You can find some example models in the `model` folder. I used the `humanoid.xml` model to test. You should see the model appear then fall to the floor face first, as if it couldn't bear to exist in this world. After spending time trying to learn to use this software, I can relate.

## Create a custom model




