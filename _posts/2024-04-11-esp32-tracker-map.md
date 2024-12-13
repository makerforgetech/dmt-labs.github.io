---
layout: post
title: Legally Not a Marauders Map
date: 2024-04-11 08:50
category: 
author: 
tags: []
summary: 
---

I made a paper map that shows where your friends and family are in the world in real-time. 
Let's take a look.

## The Idea

My wife and daughter are fans of the Harry Potter series, and there are a couple of really interesting ideas in the books that I wanted to try and recreate for them. One of these is the Marauders Map, which shows the location of everyone in Hogwarts in real time. The other is the Weasley Clock, which shows the location of each member of the Weasley family.

Since I travel around the UK for work I thought it would be fun to have a map that shows where I am in the country so that my daughter could see when I was on my way home. There are also some interesting upgrades I can add to it later, which we'll talk about in a bit.


## Equipment

For this build I wanted to go with a low power display that could project through paper, card or even canvas so that I could create a real map and have the locations projected through the map. I did some research and found this [RGB led matrix from Waveshare](https://www.waveshare.com/wiki/RGB-Matrix-P2.5-96x48-F). It has 98 x 48 pixels and is 240 x 120mm in size. It's also low power and so can be powered from a USB port.

To control the display I opted for an ESP32 microcontroller development board. The ESP32 is a powerful microcontroller that has built-in WiFi and Bluetooth capabilities. This makes it ideal for IoT projects, as it can connect to the internet and communicate with other devices wirelessly. The ESP32 also has a number of GPIO pins that can be used to connect to the display. Helpfully, the wiki for the RGB matrix also includes example code and a wiring diagram to get you started.

To retrieve the users location I wanted to use Telegram. Telegram is a messaging app that has a number of features that make it ideal for IoT projects. One of these is the ability to create bots that can be used to send and receive messages. I can create a bot that can be used to send my location to the ESP32, which can then display it on the map.

## The electronics

The first step was to create a proof-of-concept to make sure everything worked as I wanted it to. I found some code on a [GitHub repository](https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot/blob/master/examples/ESP8266/Location/Location.ino) that showed an easy example script for connecting to a Telegram bot and sending your location.

The first step was to configure a bot through Telegram's BotFather account. It uses chat based commands to make the process easy. Once the bot is created they send an API access token that you can use to connect to the bot from your code.

After setting the API key and wifi credentials I uploaded the code to the ESP32 development board and checked the debug log for output. It all looked great so I shared my location with the bot and the ESP32 displayed the latitude and longitude in the debug console as well as the chat on my phone.

The next step was to get the display working. I connected the display to the ESP32 using the wiring diagram from the wiki and uploaded the example code. There was a little bit of configuration needed to include missing libraries, and I found that the libraries needed to be the exact versions used in the sample code otherwise some of the pixels wouldn't display. I've included the full source and libraries in the [GitHub Repository](https://github.com/makerforgetech/esp32-tracker-map) for this project.

The display showed the sample patterns and text, which was a good sign that everything was working.

The next step was combining the two scripts. I added the display code to the location script and added code to convert the latitude and longitude to a pixel on the display and uploaded it to the ESP32. This works by defining a minimum and maximum latitude and longitude, basically a top left and bottom right location to use as the range of the LED matrix. 
Once these values are defined the script determines the relative location of the shared location and displays a pixel on the display at roughly the correct location.

The good thing about this approach is that if I ever want to change the scale of the map or track a new area of the world all I need to do is set the new min / max locations and the script will recalibrate.

## Assembling the Map

Rather than creating a standard map of the UK I thought it would be fun to put a twist on it, so instead of adding the locations I'm likely to be visiting I found a website that has mapped some of the locations from the Harry Potter universe. I found a blank map of the UK and added the fictional locations to that map. I then printed the map on a large sheet of parchment style paper and placed it over the LED matrix.

The result is a map of the UK with the fictional locations from the Harry Potter universe, and a pixels that move around the map as I share my location with the bot.

I also created a user whitelist, so I can share the bot with my family and friends and they can also share their location if they want to. This means that the map can show where everyone on the list is in the UK at once. 

If someone travels to a different country the location will exceed the range. For a bit of fun I created a location that was the fictional prison of Azkaban and programmed any out of range locations to display there.

Next I just needed to assemble everything into a frame. Because the map is larger than the LED matrix I took the back off the frame, marked the size of the LED matrix on the back and cut the section out. I secured it in place so that the LED matrix would fit flush against the map to ensure the LEDs would shine through the paper.

The LED matrix has a power cable that is designed to work with a bench-top power supply. The connection to the ESP32 is also a header called HUB75 that I haven't used before, but fortunately it comes with a breakout cable that makes it easy to connect to the ESP32. The problem is this isn't a very permanent connection, so I used this breakout board that allows connections to the pins via screw terminals. All I had to do was remove the headers from the breakout cable and screw the wires into the screw terminals.

Because the RGB matrix is rated at 4 amps I couldn't connect it directly to the ESP32, so I used a micro USB breakout board and created a quick custom board to power both the ESP32 and the RGB matrix. I'll only be powering a handful of pixels at once so the power draw should be much less than 4 amps in practice.

Finally I secured everything to the back of the frame with hot glue, and gave it a test.

## Upgrades

One of the exciting things about this project is that I can upgrade it over time.  Here are a few ideas I have for future upgrades:

The RGB matrix supports multiple displays chained together, so I could add more displays to create a larger map or have multiple locations covered in detail by having two separate displays at different parts of a larger map.

A microwave sensor could be installed in the frame to detect when someone is nearby and fade in the pixels. This not only saves power but also adds to the magical effect of the map.

Similarly, the person sensor from Useful Sensors is a small low power board that accurately detects faces. This could be used to detect when someone is looking at the map to only display the pixels when it is being watched.

From a software perspective, integrating with a train tracking API would allow me to show the status of the train lines that I use to travel. I could show each train's location and even colour code them to show if they're running on time or not.
An integration with a weather API could also be added to show the weather at key locations, or the country as a whole.

Finally, I like the idea of being able to swap the picture and set the display into different modes, so I could have a local map to show when we're out on walks, and then a map of the UK when I'm travelling for work.

What would you add to this project? Join the discord community and let me know.

## Close

The files for this build are available in the GitHub repository. I hope you enjoyed this build, and if you did please consider subscribing to the YouTube channel.
