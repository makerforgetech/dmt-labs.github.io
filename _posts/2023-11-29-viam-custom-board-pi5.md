---
layout: post
title: Using the Raspberry Pi 5 with Viam 
date: 2023-11-29 19:04
categories: [Robotics, Raspberry Pi, Viam]
tags: [robotics, raspberry-pi, viam]
---

The Raspberry Pi 5 is the latest single-board computer from the Raspberry Pi Foundation. It is a significant upgrade from the Raspberry Pi 4, with a new 64-bit processor, more RAM, and a new form factor.

At the time of posting, there is a limitation around the pigpio library that Viam uses to control the GPIO pins on the Raspberry Pi. This means that Viam is not currently compatible with the Raspberry Pi 5 using the default board configuration.

However, there is a workaround that allows you to use the Raspberry Pi 5 with Viam. This article will cover how to do this.

## What's the error?

If this problem affects you, you will be unable to see any board controls in the `control` tab on your Viam app. You should see an error message similar to the following when you check the logs:

```
2023-11-25T16:43:08.623Z   error   robot_server   impl/resource_manager.go:472   configuration error: reached max of 5 configuration attempts for rdk:component:board/local  
2023-11-25T16:43:03.624Z   error   robot_server   impl/resource_manager.go:621   error building resource   resource rdk:component:board/local   model rdk:builtin:pi   error error: PI_INIT_FAILED: gpioInitialise failed 
```

## Configuring a custom board

To fix this, we need to configure a custom board. This will allow us to use the Raspberry Pi 5 with Viam.

https://docs.viam.com/build/configure/components/board/customlinux/

The process is described in detail in the link above, but to summarise:

1. Create a new file on your Raspberry Pi, make a note of the file name and location, for example /home/pi/myboard.json

2. Add the following contents to the file:

```
{
  "pins": [
    {
      "name": "3",
      "device_name": "gpiochip4",
      "line_number": 2
    },
    {
      "name": "5",
      "device_name": "gpiochip4",
      "line_number": 3
    },
    {
      "name": "7",
      "device_name": "gpiochip4",
      "line_number": 4
    },
    {
      "name": "29",
      "device_name": "gpiochip4",
      "line_number": 5
    },
    {
      "name": "31",
      "device_name": "gpiochip4",
      "line_number": 6
    },
    {
      "name": "26",
      "device_name": "gpiochip4",
      "line_number": 7
    },
    {
      "name": "24",
      "device_name": "gpiochip4",
      "line_number": 8
    },
    {
      "name": "21",
      "device_name": "gpiochip4",
      "line_number": 9
    },
    {
      "name": "19",
      "device_name": "gpiochip4",
      "line_number": 10
    },
    {
      "name": "23",
      "device_name": "gpiochip4",
      "line_number": 11
    },
    {
      "name": "32",
      "device_name": "gpiochip4",
      "line_number": 12
    },
    {
      "name": "33",
      "device_name": "gpiochip4",
      "line_number": 13
    },
    {
      "name": "8",
      "device_name": "gpiochip4",
      "line_number": 14
    },
    {
      "name": "10",
      "device_name": "gpiochip4",
      "line_number": 15
    },
    {
      "name": "36",
      "device_name": "gpiochip4",
      "line_number": 16
    },
    {
      "name": "11",
      "device_name": "gpiochip4",
      "line_number": 17
    },
    {
      "name": "12",
      "device_name": "gpiochip4",
      "line_number": 18
    },
    {
      "name": "35",
      "device_name": "gpiochip4",
      "line_number": 19
    },
    {
      "name": "38",
      "device_name": "gpiochip4",
      "line_number": 20
    },
    {
      "name": "40",
      "device_name": "gpiochip4",
      "line_number": 21
    },
    {
      "name": "15",
      "device_name": "gpiochip4",
      "line_number": 22
    },
    {
      "name": "16",
      "device_name": "gpiochip4",
      "line_number": 23
    },
    {
      "name": "18",
      "device_name": "gpiochip4",
      "line_number": 24
    },
    {
      "name": "22",
      "device_name": "gpiochip4",
      "line_number": 25
    },
    {
      "name": "37",
      "device_name": "gpiochip4",
      "line_number": 26
    },
    {
      "name": "13",
      "device_name": "gpiochip4",
      "line_number": 27
    }
  ]
}
``` 
3. In your viam app, create a new board and select the `customlinux` board type. In the attributes section where the line below is shown, add your path to the file you created in step 1:

``` 
{
  "board_defs_file_path": "/home/pi/myboard.json"
}
```

4. Save the board and restart the app. You should now be able to see the board controls in the `control` tab.

## What is a custom board definition file?

A custom board definition file is a JSON file that describes the configuration of your board. It is used by Viam to understand how to control the GPIO pins on your board.

The above example was created by looking at the pinout of the Raspberry Pi 5 using the command `sudo gpioinfo` and then mapping the pin numbers to the GPIO chip and line number.

For example, in the output of that command we can see that pin 3 is on gpiochip4, line 2:

``` 
gpiochip4 - 54 lines:
	line   0:      "ID_SD"       unused   input  active-high 
	line   1:      "ID_SC"       unused   input  active-high 
	line   2:       "PIN3"       unused   input  active-high 
	line   3:       "PIN5"       unused   input  active-high 
	line   4:       "PIN7"       unused   input  active-high 
	line   5:      "PIN29"       unused   input  active-high 
	line   6:      "PIN31"       unused   input  active-high 
	line   7:      "PIN26"       unused   input  active-high 
	line   8:      "PIN24"       unused   input  active-high 
````

This is then mapped to the following entry in the custom board definition file where the `name` is the pin number and `line_number` is the GPIO pin number:

```
{
    "name": "3",
    "device_name": "gpiochip4",
    "line_number": 2
},
```
You can see how those relate to one another in the pinout diagram shown on the website pinout.xyz:

https://pinout.xyz/pinout/pin3_gpio2/

## What's next?

The limitation around the use of the standard Raspberry Pi board definition is because of the issue with the pigpio library. Once this has been updated, Viam expects the Raspberry Pi 5 to work with the standard board definition.

If you have any questions or comments in the meantime, the [Viam Community](https://www.viam.com/resources/community) can put you in touch with the Viam team who are more than happy to help.

If you have any suggested updates to the above board definition, please reach out on [this topic](https://discord.com/channels/1083489952408539288/1177642689299234888).

## References

[Custom board definition](https://docs.viam.com/build/configure/components/board/customlinux/)

[Upload a custom board definition](https://docs.viam.com/fleet/cli/#board)
