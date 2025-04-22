---
title: Pico Lightsaber
icon: fas fa-info-circle
order: 0
---

## Introducing the Modular Raspberry Pi Pico Lightsaber Controller

The **Raspberry Pi Pico Lightsaber Controller** is a modular, customizable project inspired by the Star Wars universe. Designed to resemble a lightsaber hilt, this controller combines cutting-edge microcontroller technology with creative design to deliver a versatile tool for controlling IoT devices, robotics, and more.


![Lightsaber](/assets/img/pages/lightsaber/project_lightsaber.jpg)

<div style="display: flex; justify-content: space-between;">
  <img src="/assets/img/pages/lightsaber/project_lightsaber1.jpg" height="300"/>
  <img src="/assets/img/pages/lightsaber/project_lightsaber2.jpg" height="300"/>
  <img src="/assets/img/pages/lightsaber/project_lightsaber3.jpg" height="300"/>
  <img src="/assets/img/pages/lightsaber/project_lightsaber4.jpg" height="300"/>
  <img src="/assets/img/pages/lightsaber/project_lightsaber5.jpg" height="300"/>
</div>

### Project Overview

This project was inspired by my modular biped robot, which takes cues from BD-1 in the *Star Wars: Jedi* games. The controller features:

- A **Raspberry Pi Pico** for processing and connectivity (WiFi and Bluetooth).
- **Neopixels** for dynamic lighting effects.
- A **3D-printed hilt** with modular components.
- Magnetic connectors for easy module swapping.
- An **18650 battery** with an uninterruptible power supply (UPS) module.

The modular design allows for experimentation, enabling users to swap components depending on their needs. The lightsaber aesthetic is enhanced by addressable LEDs and a custom 3D-printed design.

---

### Key Features

#### 1. **Power Management with UPS**
The controller uses an 18650 battery paired with a UPS module to ensure uninterrupted power. After experimenting with custom PCB designs, I opted for a pre-built UPS module provided by Wildware.net. This module includes:

- A charging circuit for the battery.
- A DC-DC converter for stable 5V output.
- Magnetic connectors for power and signal distribution.

#### 2. **Neopixel Integration**
The controller includes multiple Neopixel strips for customizable lighting effects. A single GPIO pin on the Pico controls all Neopixels, even when modules are swapped. The wiring ensures seamless signal flow across all modules.

#### 3. **Joystick and Button Controls**
A joystick and push button provide user input for controlling the Neopixels and other connected devices. For example:

- The joystick adjusts Neopixel colors and brightness.
- The button activates a flashlight mode, turning the emitter Neopixels to full white.

#### 4. **Presence Detection**
An RCWL microwave sensor dims the Neopixels when no motion is detected, conserving power and adding interactivity.

---

### Design and Assembly

#### **3D Modeling**
The hilt design was inspired by the "Serenity" lightsaber from *Star Wars Jedi: Survivor*. Using OnShape, I created a modular design with screw-thread connectors for easy assembly and disassembly. Key design principles included:

- Tool-free assembly.
- Avoiding glue and screws for easy component replacement.
- Compatibility with 3D-printed and aluminum parts.

#### **Custom PCB**
To simplify wiring, I designed a custom PCB with headers for magnetic connectors and Neopixel connections. PCBWay sponsored the PCB production, ensuring a professional finish.

#### **3D-Printed and Aluminum Parts**
The emitter and UPS housing were printed in aluminum for durability and aesthetics. These parts add weight and a premium feel to the build.

---

### Applications and Future Plans

The controller's modular design and connectivity options make it ideal for various applications, including:

- Controlling IoT devices via WiFi or Bluetooth.
- Operating robotics, such as my modular biped robot.
- Experimenting with new modules and features.

Future updates will include expanded functionality and additional modules for enhanced control.

---

### Resources

- **Software Repository**: [GitHub Repository](https://github.com/makerforgetech/pico-controller)
- **Bill of Materials**: [View Materials](https://github.com/makerforgetech/pico-controller?tab=readme-ov-file#bill-of-materials)
- **Assembly Video**: [YouTube Channel](https://www.youtube.com/@DanMakesThings/shorts)
- **Blog Post**: [MakerForge Blog](/posts/raspberry-pi-pico-controller/)

---

### Acknowledgments

Special thanks to **PCBWay** for sponsoring the custom PCB and aluminum parts, and to **Wildware.net** for providing the UPS module.
