---
title: Using Raspberry Pi 5 with Modular Biped
date: 2023-10-10
categories: [Robotics, Modular Robotics Framework, News and Events]
tags: [robotics, news, raspberry-pi-5]     # TAG names should always be lowercase
image: https://www.raspberrypi.com/app/uploads/2023/09/aa7841cb-421a-4000-8ab9-c77478a4f83b-2048x1365.jpg

---
_Image credit: [Raspberry Pi](https://www.raspberrypi.com/news/introducing-raspberry-pi-5/)_


The Raspberry Pi 5 was announced last month and offers a number of improvements over the model currently used in the Modular Robotics Bipedal Robot project (The Pi 3B+). Let's do a comparison to see what kind of features and issues we'd expect to see when using the new model.

## Raspberry Pi 5 vs Raspberry Pi 3B+ (Relevant Features)

| Feature | Pi 5 | Pi 3B+ |
| --- | --- | --- |
| USB | 2x USB 3.0, 2x USB 2.0 | 4x USB 2.0 |
| Camera | 2x MIPI CSI camera ports | 1x MIPI CSI camera port |
| GPIO | 40-pin GPIO header | 40-pin GPIO header |
| Power | USB-C, 5v 5A (Max) | Micro USB, 5.1v 3A (Max) |


## Power requirements

![DC-DC Converters](/assets/img/posts/2023-10-10-pi-5-deep-dive/converters.png){: .w-50 .right}

From the power requirements, we can see that the Pi 5 requires a USB-C power supply, which is a welcome change from the Micro USB connector on the Pi 3B+. This is a more robust connector and is less likely to break over time. Fortunately this doesn't impact the project as we connect the power directly to the 5v rail using the custom PCB and a DC-DC converter within the head of the robot.

One challenge would be to make sure that the DC-DC converter can produce the number of amps required for the new model. For example, the XL4015 is capable of providing 5A, but the lesser converters XL4005 or MINI360 that I have used in the past are only capable of 3.5A or 1.8A respectively. Fortunately the form factor of the XL4015 is almost identical to the XL4005 currently in place.

**Outcome:** Upgrade DC-DC converter to ensure 5V 5A is available to the Pi 5 (XL4015 is compatible)

## Form Factor

The Raspberry Pi 5 shares a similar form factor to the Pi 3B+, with the same 40-pin GPIO header and the same mounting holes. This means that the existing PCBs and 3D printed parts should be compatible with the new model. Although the ethernet and USB ports are in different locations to the Pi 4, it seems that are in the same position as the Pi 3B+.

![Pi 5 with cooling fan](https://www.raspberrypi.com/app/uploads/2023/09/91e84eee-f588-4953-ae72-693acb1fe97b.jpg){: .w-50}
_Image credit: [Raspberry Pi](https://www.raspberrypi.com/news/introducing-raspberry-pi-5/)_

One other consideration is that the Pi 5 requires a fan to keep it cool, which is not the case for the Pi 3B+. This means that the custom PCB may not fit as the fan may be blocking components. This will need to be tested.

**Outcome:** The Pi 5 should be compatible with the existing PCBs and 3D printed parts, but the fan module may block the PCB from being installed.

## Camera

The Pi 5 has two MIPI CSI camera ports, which is a welcome addition as it allows for the use of two cameras for 3D imaging and localisation. This is something that I have been looking into for a while, but the Pi 3B+ only has one camera port which limits the options.

The use of a second USB camera has always been possible, but this is not ideal for this project where anything connected to the USB ports is highly visible. 

![Pi 5 with two cameras](https://www.raspberrypi.com/app/uploads/2023/09/58f150c0-bd72-4e42-a77f-6be0890c8a80.png){: .w-50}
_Image credit: [Raspberry Pi](https://www.raspberrypi.com/news/introducing-raspberry-pi-5/)_

I'm looking forward to exploring the use of two cameras for this project and expect other makers will be doing the same.

**Outcome:** The Pi 5 has two MIPI CSI camera ports which will allow for the use of two cameras for 3D imaging and localisation.

## Power Button

To the relief of many makers, a power button has been added to the Raspberry Pi 5 to enable a safe shutdown and restart option on the single board computer. This is a welcome addition as it means that the robot can be shutdown safely without having to SSH into the Pi or use a physical switch.

Currently the modular biped project has support for an external shutdown switch which, when pressed, triggers an immediate shutdown of the running python and then Raspbian OS. Without this support the SD card can become corrupted and the Pi may not boot up again if the power is disconnected while running.

The addition of the power button may render the external switch redundant, but this will depend on the accessibility of the button when the robot is assembled.

**Outcome:** The external shutdown switch may no longer be required, but this will need to be tested.


## Increased CPU and RAM

The current version of the project relies on a Coral USB Accelerator to perform the object detection and recognition. This is a USB device that contains a TPU (Tensor Processing Unit) which is designed to perform machine learning tasks. This connects to the Pi 3B+ via USB and is used to offload the processing of the object detection and recognition to the TPU, which is much faster than the CPU on the Pi.

Without this device the Pi 3B+ would struggle to perform the object detection and recognition in real time, but the Pi 5 has a much more powerful CPU and GPU which may be able to perform these tasks without the need for the Coral USB Accelerator. 

Removing the USB Accelerator would reduce the cost of the project and save power, which would help reduce the impact of swapping the Pi 3B+ out for the more power hungry Pi 5.

**Outcome:** The Pi 5 may be able to perform the object detection and recognition in realtime without the need for the Coral USB Accelerator.

## Conclusion

The Raspberry Pi 5 is a welcome upgrade to the Pi 3B+ and offers a number of improvements that will benefit the Modular Biped project. The main benefits are the dual MIPI CSI camera ports, the increased CPU and RAM, and the power button.

For very little effort, the project could be upgraded to use the Pi 5 and this would allow for the use of two cameras for 3D imaging and localisation. The increased CPU and RAM may also allow for the removal of the Coral USB Accelerator, which would reduce the cost and power consumption of the project.

I will be testing the Pi 5 with the Modular Biped project as soon as I can get my hands on one, so watch this space!