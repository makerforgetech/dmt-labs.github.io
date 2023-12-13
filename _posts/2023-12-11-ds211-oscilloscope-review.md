---
layout: post
title: DS211 Mini Oscilloscope - Miniware
date: 2023-12-16
categories: [Products, Reviews]
tags: [review, oscilloscope, miniware]
image: /assets/img/posts/2023-12-11-ds211-oscilloscope-review/box.jpg
---

Miniware very kindly sent me a DS211 Mini Oscilloscope to review. In this post I'll be sharing my thoughts on the device, and how it can be used in your own projects.

## Introduction and Features

The DS211 mini oscilloscope is a single channel digital oscilloscope with a dedicated display and an integrated battery for portability. This device has been designed with makers and robotics enthusiasts in mind and is equipped to allow seamless data exchange with computers through the Micro USB interface.

Featuring a 320x240 resolution LCD display, the DS211 mini oscilloscope is also compact. Its user-friendly design ensures ease of operation, making it a useful tool for both hobbyists and seasoned professionals.

![Unboxing the DS211 Mini Oscilloscope](/assets/img/posts/2023-12-11-ds211-oscilloscope-review/unboxing_complete.jpg){:.w-50}

The device comes with a built-in rechargeable 500mAH battery, providing around 3 hours of continuous functionality. With a maximum sampling rate of 1MHz, the DS211 is well-suited for school experiments, home appliance repair, and electronic engineering.

## Uses and Applications

#### Education

Ideal for educational purposes, the DS211 allows students to visualize and understand electronic signals in real-time. Its compact size and user-friendly interface make it a perfect companion for classroom experiments, helping students grasp fundamental concepts in electronics.

#### Home Appliance Repair

When troubleshooting and repairing home appliances, a single-channel oscilloscope proves invaluable. The DS211 enables users to analyze waveforms, voltage levels, and signal integrity, aiding in the identification and resolution of issues within electronic circuits, ensuring a more accurate and efficient repair process.

#### Electronic Prototyping

Makers and robotics enthusiasts can utilize the DS211 for prototyping electronic circuits. The oscilloscope's ability to display waveforms and capture signals is crucial during the development phase, allowing users to fine-tune and optimize their designs for optimal performance.

#### Sensor and Actuator Testing

In robotics and automation projects, understanding the behavior of sensors and actuators is essential. The single-channel oscilloscope can be employed to analyze sensor output signals and ensure precise control of actuators, contributing to the overall reliability and efficiency of robotic systems.

#### Signal Integrity Analysis

Engineers working on electronic projects benefit from the DS211's ability to analyze signal integrity. Whether assessing the quality of a digital communication signal or troubleshooting signal distortion, the oscilloscope provides valuable insights into the performance of electronic components and circuits.

#### Audio System Debugging

Audio enthusiasts can use the oscilloscope for debugging and optimizing audio circuits. It allows for the visualization of audio waveforms, aiding in the identification of distortion, noise, or frequency-related issues in audio systems and amplifiers.

#### Pulse Width Modulation (PWM) Analysis

For projects involving PWM, such as motor control or LED dimming, the DS211 enables users to analyze and fine-tune pulse-width-modulated signals. This is crucial for achieving precise control over various applications, enhancing the performance of robotics and automation projects.

## Detailed Technical Specs

![DS211 Mini Oscilloscope](/assets/img/posts/2023-12-11-ds211-oscilloscope-review/specs.jpg)
_Image Source: [E-Design](https://e-design.com.cn/en/Mini-Oscilloscope-DS211-PG9226722)_

## First Impressions

### Packaging and Quality
The device arrives well packaged with a Micro USB cable, an MCX probe, and a user manual.
The device's build quality is good, and the buttons feel sturdy and responsive. The interface takes a little getting used to, but it's easy to navigate with the help of the user manual.

### Battery

The devices is partially charged on arrival, but it's a good idea to charge it fully before use. The battery life is impressive, and the device can be used for several hours on a single charge.

### Menu and Settings

The menu allows for a range of settings to be adjusted, including the time-frame, trigger level, and trigger mode. The device also features a built-in signal generator, which can be used to generate sine, square, sawtooth and triangle waves.

Although the menu is accessible via the buttons on the device, the titles of each menu item are abbreviated. This can make it difficult to navigate the menu without the user manual. However, the device is easy to use once you're familiar with the menu structure.

The menu options are as follows:

- Yn: Y-axis function settings
- Xn: X-axis function settings
- Tr: Trigger function settings
- Me: Measurement function settings
- Ex: Waveform calculation function settings
- Fn: Saving and loading function settings
- Sn: Waveform output parameter settings
- St: System function settings

The firmware on the device can be upgraded, this is achieved by downloading a file to your PC and transferring it to the device by USB (it appears as a removable drive when booted into that mode). The upgrade process is straightforward and takes a few minutes to complete.

### Probe

The device includes two connections for the probe, a signal input and signal output (for the signal generator). The probe is MCX, so it's easy to replace if necessary. Note that this is a different connection from the standard BNC probe used on many full-sized oscilloscopes. Adaptors can be purchased separately to convert between the two types if needed.

The probe allows switching between 1X and 10X attenuation, which is useful for measuring higher voltages. The probe is also equipped with a ground clip, which can be used to connect to the ground of the circuit being measured.

Bandwidth and sample rate on an oscilloscope determines the range of frequencies and the number of data points captured, respectively, impacting the oscilloscope's ability to accurately display and analyze electronic signals.
200KHz Bandwidth and only 1M Sample rate is a little low, but is sufficient for less complex work. However, this is to be expected for a device of this size and price.

Similarly, there is only one probe so you aren't able to use the signal generator and probe at the same time, or compare multiple signals at once.

Despite these limitations, the device is still a competent and useful tool. Let's take a look at an example of how it can be used.

## Testing

As a simple test of the oscilloscope, I created a simple circuit using an Arduino Uno and an LED (and resistor - don't forget that!). The Arduino was programmed with the script below, that would gradually increase and decrease the brightness of the LED.

```c
int led = 10;           // the PWM pin the LED is attached to
int brightness = 0;    // how bright the LED is
int fadeAmount = 5;    // how many points to fade the LED by

// the setup routine runs once when you press reset:
void setup() {
  // declare pin 9 to be an output:
  pinMode(led, OUTPUT);
}

// the loop routine runs over and over again forever:
void loop() {
  // set the brightness of pin 9:
  analogWrite(led, brightness);

  // change the brightness for next time through the loop:
  brightness = brightness + fadeAmount;

  // reverse the direction of the fading at the ends of the fade:
  if (brightness <= 0 || brightness >= 255) {
    fadeAmount = -fadeAmount;
  }
  // wait for 3 seconds to see the dimming effect
  delay(3000);
}
```

Then, by connecting the probe to the positive side of the LED and connecting the ground clip to the Arduino's ground, I was able to measure the voltage across the LED.

![PWM Signal on DS211 Mini Oscilloscope](/assets/img/posts/2023-12-11-ds211-oscilloscope-review/pwm.jpg)

The above image shows the output after some (easy) adjustment of the X and Y axis settings. We can clearly see the PWM signal.

## Conclusion

In summary, the DS211 mini oscilloscope, despite being a single-channel device, is a multifaceted tool with broad applications. From educational settings to hands-on projects and professional tasks, its portability, ease of use, and reliable performance make it an essential instrument for anyone involved in electronics and robotics.

![DS211 Mini Oscilloscope](/assets/img/posts/2023-12-11-ds211-oscilloscope-review/on_w_box.jpg){:.w-50}

This device is a great first oscilloscope for beginners and a handy tool for professionals. It can't replicate the functionality of a full-sized oscilloscope, but it's a great addition to any maker's toolkit.

If you'd like to purchase one for yourself, you can find it on [AliExpress](https://www.aliexpress.us/item/3256805247338830.html). 

For more information, check out the manufacturer's website [here](https://e-design.com.cn/en/Mini-Oscilloscope-DS211-PG9226722).