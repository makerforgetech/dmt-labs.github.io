---
title: Magic Map
icon: fas fa-info-circle
order: 0
---

# "Legally Not a Marauders Map"

Imagine a map that shows the real-time locations of your friends and family, projected onto a physical surface. Inspired by the magical elements of the Harry Potter universe, this project brings the concept of the Marauders Map to life using modern technology.

![Magic Map](/assets/project_map.jpg)

## The Inspiration

The idea for this project came from two iconic magical artifacts: the Marauders Map, which shows the location of everyone in Hogwarts, and the Weasley Clock, which tracks the whereabouts of family members. As someone who frequently travels across the UK for work, I wanted to create a map that my daughter could use to see where I am and when I’m on my way home. This project also opens the door to exciting upgrades and customizations.

## The Technology Behind the Map

### Display and Microcontroller

The core of the project is an [RGB LED matrix from Waveshare](https://www.waveshare.com/wiki/RGB-Matrix-P2.5-96x48-F), which features 96 x 48 pixels and is compact enough to fit behind a paper map. It’s low-power and USB-powered, making it ideal for this application.

To control the display, I used an ESP32 microcontroller. The ESP32 is perfect for IoT projects, thanks to its built-in WiFi and Bluetooth capabilities. It also has enough GPIO pins to interface with the LED matrix. The Waveshare wiki provided helpful example code and wiring diagrams to get started.

### Location Tracking with Telegram

For location tracking, I leveraged Telegram’s bot functionality. Telegram bots can send and receive messages, making them a great fit for IoT projects. I created a bot using Telegram’s BotFather, which provided an API token for integration. The bot allows users to share their location, which the ESP32 then processes and displays on the map.

### Combining the Components

The project began with a proof-of-concept: connecting the ESP32 to the Telegram bot and displaying location data in the debug console. Once that was working, I integrated the LED matrix. By defining a minimum and maximum latitude and longitude, the script maps real-world coordinates to pixels on the display. This approach makes it easy to recalibrate the map for different regions.

## Building the Map

To give the map a unique twist, I overlaid fictional Harry Potter locations onto a blank map of the UK. The map was printed on parchment-style paper and placed over the LED matrix. The result is a magical-looking map that updates in real-time as users share their locations.

The hardware was assembled into a frame, with the LED matrix secured behind the map. A custom power solution ensures the ESP32 and LED matrix are powered reliably. For added durability, I used a breakout board with screw terminals to connect the matrix to the ESP32.

## Future Upgrades

This project is just the beginning. Here are some ideas for future enhancements:

- **Chained Displays**: Add more LED matrices to cover larger areas or create detailed maps of specific regions.
- **Proximity Sensors**: Use a microwave or face-detection sensor to activate the map only when someone is nearby.
- **Train Tracking Integration**: Display real-time train locations and statuses using a train tracking API.
- **Weather Data**: Show weather conditions at key locations using a weather API.
- **Swappable Maps**: Create different map overlays for various use cases, such as hiking or international travel.

## Conclusion

This project combines creativity and technology to bring a magical concept to life. The full source code and libraries are available in the [GitHub repository](https://github.com/makerforgetech/esp32-tracker-map). If you have ideas for upgrades or want to share your own version of the project, join the discussion in our Discord community!

## References

- **GitHub Repository**: [ESP32 Tracker Map](https://github.com/makerforgetech/esp32-tracker-map)
- **Telegram Bot API**: [Telegram BotFather](https://core.telegram.org/bots#botfather)
- **Blog Post**: [MakerForge Blog](/posts/esp32-tracker-map/)