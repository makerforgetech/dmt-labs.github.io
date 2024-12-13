---
layout: post
title: Flashing a Raspberry Pi CM5 with Ubuntu 20.04
date: 2024-12-12 17:52
category: [Raspberry Pi Compute Module 5]
tags: [guide, cm5, errors]
---

I recently purchased a Raspberry Pi Compute Module 5 (CM5) and wanted to flash it with Ubuntu 20.04, but was faced with an error when trying to flash the image. This post will show you how to flash a Raspberry Pi CM5 with Ubuntu 20.04.


## Prerequisites

- Raspberry Pi Compute Module 5 (not the Lite version as this uses an SD card)
- Raspberry Pi Compute Module 5 IO Board

## Steps

Follow the steps in the official Raspberry Pi documentation (see below). The section titled 'Set up the host device' uses the `rpiboot` tool to flash the image to the CM5. However, I encountered an error when trying to flash the image using `rpiboot`.

[Guide](https://www.raspberrypi.com/documentation/computers/compute-module.html#set-up-the-host-device)

When I attempted this, the `rpiboot` tool would hang at the following message:

```
Waiting for BCM2835/6/7/2711...
```

To fix this issue, I had to compile the `rpiboot` tool from source. Here are the steps I followed:

```bash
sudo apt remove rpiboot
```

and compiled following the instruction from: https://github.com/raspberrypi/usbboot

```bash
sudo apt install git libusb-1.0-0-dev pkg-config build-essential
git clone --recurse-submodules --shallow-submodules --depth=1 https://github.com/raspberrypi/usbboot
cd usbboot
make
sudo ./rpiboot
```

I got the error:

```
2712: Directory not specified using default /usr/share/rpiboot/mass-storage-gadget64/
read_file: Failed to read "2712/bootcode5.bin" from "/usr/share/rpiboot/mass-storage-gadget64//bootfiles.bin" - No such file or directory
Failed to open bootcode5.bin
```

after specifying the directory I was able to flash the device

```bash
sudo ./rpiboot -d ./mass-storage-gadget64
```

[Source](https://forums.raspberrypi.com/viewtopic.php?t=380527)

## Read More

[Raspberry Pi CM5 IO Board Datasheet](https://datasheets.raspberrypi.com/cm5/cm5io-datasheet.pdf)