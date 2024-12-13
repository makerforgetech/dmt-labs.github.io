---
layout: post
title: Building a Raspberry Pi Pico Controller
date: 2024-11-29 22:18
categories: [Guides]
tags: [guides, raspberry pi, pico]
image: /assets/img/posts/2024-11-29-raspberry-pi-pico-controller/thumb.jpg

---

The Raspberry Pi Pico is a powerful microcontroller that can be used for a variety of projects. I recently began a build to create a controller to interface with projects via the Pico's wifi and bluetooth capabilities, with added neopixels for visual feedback. Let's take a look.

## Overview

As my modular biped robot is inspired by BD-1 from the Star Wars: Jedi games, I thought it would be good to keep that theme going for this modular controller.
I designed the project to resemble a lightsaber hilt, with neopixels spread out along each module so that various effects could be displayed.

The project includes an 18650 battery connected to an uninteruptable power supply (UPS) module, a Raspberry Pi Pico, and a selection of addressable LED neopixels. The controller is housed in a 3D printed case with a button and joystick for user input.

I wanted to create a design that would allow me to experiment with modules, so that they could be swapped as needed depending on the project. I opted to use magnetic connectors to create a 5v bus and a pair of wires to connect the Pico to the neopixels in each module.

## Designing the UPS module.

Initially I wanted to create my own power management board with a custom PCB that would convert the battery voltage from the 18650 to 5v, and also allow charging via a TP4056 charging module. Both modules are cheap and easy to source. I added them to a perfboard, but there were a couple of issues.

The first problem was that the TP4056 is not designed to charge the battery if there is a load on the output, in other words, the project needs to be switched off while charging. Normally that isn't a problem but in this instance I wanted the project to be always on, and allow the battery to work as an uninteruptable power supply, so that the battery supplies power when disconnected from the charger.

The second issue was just that the custom board didn't fit very well within the project, so I started looking at alternatives.

There are a selection of 18650 UPS modules available from various suppliers, and they looked promising. Unfortunately I had huge trouble getting an order to go through, presumably because of stock issues I had three separate orders cancelled.

Luckily, I was contacted by Zach from Wildware.net who offered to help. The project had been on hold for weeks because of this issue so it was great to get things moving again. If you'd interested in getting one of these modules for yourself I'll include the link to the listing on the wildware website in the bill of materials.

Once the module arrived I began to experiment.

The board is comprised for 3 main areas: 
- The charging module for the battery, with a 5v input (either by USB-C or direct header connection) and the battery connection. 
- The DC-DC converter to ouput a fixed 5v from the battery voltage.
- A pair of headers in the center of the board that are used to enable the DC-DC converter. This means you can attach a switch to kill the power to the converter when the board isn't in use and prevent drain of the battery as much as possible.

The board also has a 18650 battery holder in place. This is normally perfectly useable, but for this project I wanted to remove some of the bulk, so I de-soldered that holder, removed the connectors, then soldered them in place directly.

Next, I designed and 3d printed a holder for the board an battery to make the UPS module as slim as possible.

### UPS Wiring

The wiring became complex for a couple of reasons, I needed to connect power to a couple of neopixel strips that would sit on either side of the module, and also to the magnetic connectors that would feed power to the other modules in the project.

The magnetic connectors can be ordered pre-wired, so I just needed to connect all the 5v wires together to the output of the converter, and the same for the negative wires.

The connectors also have two signal wires, which allows me to create a loop throughout the entire project from the Raspberry Pi Pico GPIO output, through all of the neopixel modules in series. This way a single GPIO pin can control all of the neopixels even if the modules are swapped and changed while the project is powered.

To finish the module wiring, I connected the input signal wire from the top magnetic connector to the neopixels in this module, then to the signal wire of the bottom magnetic connector so the signal could continue out of the module. Finally I wired the other 'return' signal wires together, to allow the signal to pass unhindered back to the upper modules.

If that sounds confusing, imagine how I felt! See the diagram below for more information.

[UPS WIRING DIAGRAM](https://github.com/makerforgetech/pico-controller/blob/main/ups_wiring.drawio.svg)

## Pico Module

Next I needed to test that the setup connected the neopixels to the pico. I create a test rig that allowed me to screw a magnetic connector to a breadboard, wired up the raspberry pi and uploaded a basic script to change all of the neopixels to the same colour.

The pico module has a couple of functions that I wanted to talk about.

The basic functionality allows the colour and brightness of all of the neopixels to be changed using a small joystick module connected to a couple of pins on the pico. This was quite simple to create and I even added a debounce to the code so that multiple colour changes couldn't happen at once with a single action.

I added another push button for a secondary behaviour, and linked that to action to turn the controller into a flashlight by fading all the neopixels except the emitter, and changing the emitter to full white.

Finally, I wanted to include some kind of presence sensor so that I could dim the neopixels if no motion was detected. For that I attached an RCWL microwave sensor. This sensor takes a 5v input but includes a 3v output which I used to power the joystick. I could also have connected the joystick to the 3v bus on the pico, as an alternative.

Once I assembled this wiring I found that everything worked as expected, with a few tweaks. The problem was that the wiring was difficult to reproduce, and I wanted others to be able to follow this build themselves, so I jumped into KiCad and designed a PCB. 

This new PCB included all of the connections so that the modules could be soldered in place without complicate wiring. I also included headers to connect the same magnetic connectors to complete the board.

PCBWay kindly offered to sponsor the creation of this PCB.

## 3D Modelling

At this point I was keen to see how this would work as a self-contained module, so I got to work creating a 3d design to house it all. Because I wanted to base the design on something from the Star Wars universe I found a paid 3D model for the 'serenity' lightsaber from Star Wars Jedi Survivor and printed it so I could get a feel for what works and what I'd like to change. Then I started designing a real-world version, using OnShape.

I'll admit it took me much longer than I expected to create this design. I had a few rules that I wanted to follow that definitely made it harder, like including the ability to assemble and dissassemble the main modules without using tools. That definitely limited my options, but it was a good challenge to improve my 3D modelling skills.

Similarly, I wanted to avoid glue and screws wherever possible so that the components could be removed easily.

I also wanted to include a 3d printed screw thread connector between modules so that I could create a standard design pattern for adding or swapping modules in future. This took a few attempts to get the right tolerances for my 3D printer, but now these modules screw together easily and feel stable when they're connected.

The current versions looks something like this. There are definitely improvements I'd like to make, but the good thing is I can re-design and print new modules as often as I need to.

Each module includes the same 4 pin magenetic connectors on the top and bottom (unless they're an end piece), so they can be stacked and swapped.

The emitter includes a smaller screw thread that allows it to be swapped, and when I started testing I could see that this white PLA piece would let the light from the emitter neopixels bleed through, which I wasn't pleased with. I reached out again to PCBWay and they offered to re-print not only the emitter, but the surround for the UPS in alluminium.

These pieces have a great finish and make the whole build feel more sturdy and realistic. I love how they create the illusion of an internal core that runs through the hilt and the metal makes the build heavier and more satisfying to use. 

The process was easy to follow by using the STL files I already generated for the prints, dropping them into the website and then completing the order. 

If you're looking to print this yourself I'd definitely recommend following this plan and printing those pieces in aluminium. Take a look at the PCBWay website for pricing.

## Assembly

Once everything was printed the assembly was straightforward and didn't need any tools or glue, which is a big change from my other projects!

## Next Steps

I mentioned earlier that this was a Pico controller, but what exactly will it control? Well the good news is that the Pico includes Wifi and Bluetooth modules, so you can easily write code to connect to other IOT devices and send or recieve commands, so the plan is that I can use this build to control other builds with the joystick and buttons. It also means that I can add more control options in future if I want to, so watch this space for future changes.

I'd love to be able to connect the joystick to my modular biped and use it to control the head, animations, or even the legs in future.

## Software and Repository

The software for the project is written in MicroPython and is available in the following repository:

[Repository](https://github.com/makerforgetech/pico-controller)

## Materials

Take a look at the bill of materials for this project to get an idea of what you'll need to build your own Raspberry Pi Pico controller:

[Bill of Materials](https://github.com/makerforgetech/pico-controller?tab=readme-ov-file#bill-of-materials)

## Assembly

I will create a detailed video of the assembly and printing process for this project, so stay tuned for updates!

[YouTube](https://www.youtube.com/@DanMakesThings/shorts)

## Thanks

Thanks again to PCBWay for providing the custom PCB and 3D printed aluminium parts for this build, and to Wildware for the UPS module.