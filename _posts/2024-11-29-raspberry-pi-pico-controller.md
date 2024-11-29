---
layout: post
title: Building a Raspberry Pi Pico Controller
date: 2024-11-29 22:18
categories: [Guides]
tags: [guides, raspberry pi, pico]
image: /assets/img/posts/2024-11-29-raspberry-pi-pico-controller/thumb.jpg

---

The Raspberry Pi Pico is a powerful microcontroller that can be used for a variety of projects. I recently began a build to create a controller to interface with projects via the Pico's wifi and bluetooth capabilities, with added neopixels for visual feedback. This guide will walk you through the process of building your own Raspberry Pi Pico controller.

## Design

The project is a self contained controller that includes an 18650 battery connected to an uninteruptable power supply (UPS) module, a Raspberry Pi Pico, and a selection of addressable LED neopixels. The controller will be housed in a 3D printed case with a 3D printed button for user input.

I wanted to create a design that would allow me to experiment with modules, so that they could be swapped as needed depending on the project. I opted to use magnetic connects to create a 5v bus and a pair of wires to connect the Pico to the neopixels in each module.

Inspired by lightsabers from Star Wars, I designed the project to resemble a lightsaber hilt, with the neopixels spread out along each module so that various effects could be displayed.


## Software and Repository

The software for the project is written in MicroPython and is available in the following repository:

[Repository](https://github.com/makerforgetech/pico-controller)

## Materials

Take a look at the bill of materials for this project to get an idea of what you'll need to build your own Raspberry Pi Pico controller:

[Bill of Materials](https://github.com/makerforgetech/pico-controller?tab=readme-ov-file#bill-of-materials)

## Assembly

I will create a detailed video of the assembly and printing process for this project, so stay tuned for updates!

[YouTube](https://www.youtube.com/@DanMakesThings/shorts)



