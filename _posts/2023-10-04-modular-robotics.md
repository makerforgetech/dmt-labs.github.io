---
title: Modular Robotics with Raspberry Pi and Python
date: 2023-10-03
categories: [Guides, Robotics]
tags: [modular, robotics, framework]     # TAG names should always be lowercase
image: /assets/img/posts/2023-10-04-modular-robotics/deck.png
---

As a project to keep myself sane during the COVID lockdowns, I began looking into making a companion robot using Raspberry Pi, Arduino and 3D printing as a study in how to build a desktop companion robot. I wanted to make something that was fully autonomous and responded to the presense and interaction of people in the room. It also needed to be reasonably affordable (as much as robotics can be!) and accessible.

The project has evolved over the last few years and a community has begun to grow around it. This led to a number of changes to allow the platform to be more modular, allowing any number of robots to be created using the software with reuse of any relevant hardware components and modules.

## Overview

![Full project (front view)](/assets/img/posts/2023-10-04-modular-robotics/full_project_front_thumb.jpg)

The Modular Biped Robot project is designed to provide a flexible and modular framework for robotics development using Python and C++ on the Raspberry Pi and Arduino platforms. It aims to enable developers, robotics enthusiasts, and curious individuals to experiment, create, and customize their own biped robots. With a range of features and functionalities and the option to add your own easily, the Modular Biped Robot Project offers an exciting opportunity to explore the world of robotics.

## Modularity

The project is designed to be modular, meaning any components that are not required for the implementation can be removed and the functionality can be disabled by removing the initialisation in the `main.py` python file. Similarly, new modules can be integrated as easily by following the guide below.

[Modules](https://github.com/dmt-labs/modular-biped/wiki/Software:-Modules)

[Creating a Module](https://github.com/dmt-labs/modular-biped/wiki/Software:-Creating-a-module)

## Versioning

There have been 3 versions of this project, version 2 was the smaller and less stable build detailed in the playlist below. 

[Watch the introduction on YouTube](https://youtu.be/Nqp4vuDWgpw?si=W-4mwJQCqWwtEBne)

[Companion Robot v2 Playlist](https://www.youtube.com/watch?v=2DVJ5xxAuWY&list=PL_ua9QbuRTv6Kh8hiEXXVqywS8pklZraT)

The current development is ongoing on version 3. This documentation is exclusively for version 3.

## Platforms

Although the project was built on the Raspberry Pi 3B+ and Arduino Pro Mini, community members are working on the integration of other platforms such as the Jetson Nano, Raspberry Pi 4 & 5, Arduin Nano and the DFRobot UniHiker SBC.

We plan on sharing any relevant files to support these platforms so feel free to join the community and share your experience / requirments.

## Contributions and Discussions

We encourage active participation, contributions, and discussions from the Modular Biped Robot community. If you have any questions, ideas, suggestions, or would like to share your experiences, you can join our [GitHub Discussions](../discussions) section dedicated to the project. It's a great place to engage with other community members, exchange knowledge, and collaborate on the development of biped robotics.

Read more here: [Community](../discussions/17)

We look forward to your participation and the exciting advancements we can achieve together in the field of modular biped robots!

## Get Started

The wiki and files are available on the GitHub repository linked below:

[Modular Biped Wiki](https://github.com/dmt-labs/modular-biped/wiki)

[Repository](https://github.com/dmt-labs/modular-biped)

[Discussions](https://github.com/dmt-labs/modular-biped/discussions)