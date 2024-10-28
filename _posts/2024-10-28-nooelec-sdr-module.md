---
layout: post
title: Nooelec SDR with Modular Biped for Real-Time 433 MHz IoT Signal Processing
date: 2024-10-28 11:50
categories: [Guides, Software, SDR]
tags: [guide, SDR, IoT, python]
---

As the IoT landscape expands, more devices communicate on the 433 MHz frequency, a common frequency for home automation systems, remote controls, weather stations, and similar IoT devices. With the Modular Biped project, we can leverage an RTL-SDR USB dongle from Nooelec to scan for and process these 433 MHz transmissions, making data accessible for other modules through Python's PubSub library.

This article introduces a custom Python module to interface with the Nooelec SDR, enabling data collection, processing, and publication over a PubSub bus. We’ll cover the SDR module setup, the configuration file, and example output to show how the module interprets and broadcasts data from nearby IoT devices.

### Module Overview

The `RTLSDR` Python module, created for the Modular Biped project, uses the `rtl_433` software to collect data and re-broadcasts information on detected IoT signals. This module starts by initiating an HTTP stream from `rtl_433` and listens to messages. Once configured, it is straightforward to publish or subscribe to specific topics via PubSub.

Here’s a breakdown of the components:

1. **Configuration File:** Defines the IP and port for the HTTP stream, topics for subscribing and publishing data, and dependencies.
2. **Python Module:** Manages the RTL-SDR's processes and parses incoming data for real-time analysis and publication.
3. **Output Example:** Shows how the module captures data like temperature, humidity, and battery status from various IoT devices.

### Configuration File

The configuration file defines key settings and dependencies for the `RTLSDR` module:

```yaml
rtl_sdr:
  enabled: true
  path: modules.network.rtlsdr.RTLSDR
  config:
    udp_host: "127.0.0.1"
    udp_port: 8433
    timeout: 70
    topics:
      publish_data: "sdr/data"
      subscribe_listen: "sdr/listen"
      subscribe_start: "sdr/start"
      subscribe_stop: "sdr/stop"
  dependencies:
    unix:
      - "rtl-433"
    python:
      - "pypubsub"
      - "requests"
```

- **Host and Port**: Specifies the IP and port for the `rtl_433` HTTP stream.
- **Timeout**: Sets a response timeout of 70 seconds.
- **Topics**: Defines PubSub topics for starting, stopping, and publishing data.
- **Dependencies**: Lists system and Python dependencies required by the module.

### Python Module Code

The following Python code uses the `rtl_433` tool to listen for 433 MHz signals, convert JSON data from these signals, and publish them over a PubSub network:

```python
#!/usr/bin/env python3

import requests
import json
import subprocess
from time import sleep
from pubsub import pub

class RTLSDR:
    def __init__(self, **kwargs):
        self.udp_host = kwargs.get('udp_host', "127.0.0.1")
        self.udp_port = kwargs.get('udp_port', 8433)
        self.timeout = kwargs.get('timeout', 70)
        self.topics = kwargs.get('topics')
        self.rtl_process = None
        pub.subscribe(self.start_rtl_433, self.topics['subscribe_start'])
        pub.subscribe(self.listen_once, self.topics['subscribe_listen'])
        pub.subscribe(self.stop_rtl_433, self.topics['subscribe_stop'])

    def start_rtl_433(self):
        """Starts the rtl_433 process with HTTP (line) streaming enabled."""
        if self.rtl_process is None:
            try:
                self.rtl_process = subprocess.Popen(
                    ["rtl_433", "-F", f"http://{self.udp_host}:{self.udp_port}"],
                    stdout=subprocess.PIPE,
                    stderr=subprocess.PIPE
                )
                print(f"Started rtl_433 on {self.udp_host}:{self.udp_port}")
            except FileNotFoundError:
                print("rtl_433 command not found. Please ensure rtl_433 is installed.")
        else:
            print("rtl_433 is already running.")

    def stop_rtl_433(self):
        """Stops the rtl_433 process if it is running."""
        if self.rtl_process:
            self.rtl_process.terminate()
            self.rtl_process.wait()
            self.rtl_process = None
            print("Stopped rtl_433 process.")
        else:
            print("rtl_433 is not currently running.")

    def stream_lines(self):
        """Stream lines from rtl_433's HTTP API."""
        url = f'http://{self.udp_host}:{self.udp_port}/stream'
        headers = {'Accept': 'application/json'}
        try:
            response = requests.get(url, headers=headers, timeout=self.timeout, stream=True)
            print(f'Connected to {url}')
            for chunk in response.iter_lines():
                yield chunk
        except requests.ConnectionError:
            print("Failed to connect to rtl_433 HTTP stream.")
            self.stop_rtl_433()

    def handle_event(self, line):
        """Process each JSON line from rtl_433."""
        try:
            data = json.loads(line)
            print(data)
            pub.sendMessage(self.topics['publish_data'], data=data)
            label = data.get("model", "Unknown")
            if "channel" in data:
                label += ".CH" + str(data["channel"])
            elif "id" in data:
                label += ".ID" + str(data["id"])

            if data.get("battery_ok") == 0:
                print(f"{label} Battery empty!")
            if "temperature_C" in data:
                print(f"{label} Temperature: {data['temperature_C']}°C")
            if "humidity" in data:
                print(f"{label} Humidity: {data['humidity']}%")

        except json.JSONDecodeError:
            print("Failed to decode JSON line:", line)

    def listen_once(self):
        """Listen to one chunk of the rtl_433 stream."""
        for chunk in self.stream_lines():
            chunk = chunk.rstrip()
            if chunk:
                self.handle_event(chunk)

    def rtl_433_listen(self):
        """Listen to rtl_433 messages in a loop until stopped."""
        self.start_rtl_433()
        try:
            while True:
                try:
                    self.listen_once()
                except requests.ConnectionError:
                    print("Connection failed, retrying in 5 seconds...")
                    sleep(5)
        finally:
            self.stop_rtl_433()

if __name__ == "__main__":
    try:
        sdr = RTLSDR(topics={
            'subscribe_listen': 'sdr/listen',
            'publish_data': 'sdr/data',
            'subscribe_start': 'sdr/start',
            'subscribe_stop': 'sdr/stop'
            })
        sdr.rtl_433_listen()
    except KeyboardInterrupt:
        print('\nExiting.')
        sdr.stop_rtl_433()
```

### Example Output

Below is an example of how the module interprets and outputs data. This example includes different devices detected by the SDR dongle, showing each device's model, temperature, humidity, and other metadata:

```shell
archie@archie:~/modular-biped $ python modules/network/rtlsdr.py 
Started rtl_433 on 127.0.0.1:8433
Connected to http://127.0.0.1:8433/stream
{'time': '2024-10-28 11:39:04', 'model': 'Esperanza-EWS', 'id': 11, 'channel': 2, 'battery_ok': 1, 'temperature_F': 67.6, 'humidity': 0, 'mic': 'CRC'}
Esperanza-EWS.CH2 Humidity: 0%
{'time': '2024-10-28 11:39:57', 'model': 'Esperanza-EWS', 'id': 11, 'channel': 2, 'battery_ok': 1, 'temperature_F': 67.6, 'humidity': 0, 'mic': 'CRC'}
Esperanza-EWS.CH2 Humidity: 0%
{'time': '2024-10-28 11:40:50', 'model': 'Esperanza-EWS', 'id': 11, 'channel': 2, 'battery_ok': 1, 'temperature_F': 67.6, 'humidity': 0, 'mic': 'CRC'}
Esperanza-EWS.CH2 Humidity: 0%
{'time': '2024-10-28 11:41:28', 'model': 'Renault', 'type': 'TPMS', 'id': 'fa6ec6', 'flags': '05', 'pressure_kPa': 192.0, 'temperature_C': -30, 'mic': 'CRC'}
Renault.IDfa6ec6 Temperature: -30°C
{'time': '2024-10-28 11:42:36', 'model': 'Esperanza-EWS', 'id': 11, 'channel': 2, 'battery_ok': 1, 'temperature_F': 67.4, 'humidity': 0, 'mic': 'CRC'}
Esperanza-EWS.CH2 Humidity: 0%
{'time': '2024-10-28 11:43:06', 'model': 'Renault', 'type': 'TPMS', 'id': 'fa6ec6', 'flags': '05', 'pressure_kPa': 192.0, 'temperature_C': -30, 'mic': 'CRC'}
Renault.IDfa6ec6 Temperature: -30°C
{'time': '2024-10-28 11:43:06', 'model': 'Renault', 'type': 'TPMS', 'id': 'fa6ec6', 'flags': '05', 'pressure_kPa': 192.0, 'temperature_C': -30, 'mic': 'CRC'}
Renault.IDfa6ec6 Temperature: -30°C
{'time': '2024-10-28 11:43:07', 'model': 'Renault', 'type': 'TPMS', 'id': 'fa6ec6', 'flags': '05', 'pressure_kPa': 192.0, 'temperature_C': -30, 'mic': 'CRC'}
Renault.IDfa6ec6 Temperature: -30°C
{'time': '2024-10-28 11:43:07', 'model': 'Renault', 'type': 'TPMS', 'id': 'fa6ec6', 'flags': '05', 'pressure_kPa': 192.0, 'temperature_C': -30, 'mic': 'CRC'}
Renault.IDfa6ec6 Temperature: -30°C
{'time': '2024-10-28 11:43:10', 'model': 'Truck', 'type': 'TPMS', 'id': '50000c66', 'wheel': 239, 'pressure_kPa': 0, 'temperature_C': 0, 'state': 1, 'flags': 10, 'mic': 'CHECKSUM'}
Truck.ID50000c66 Temperature: 0°C
{'time': '2024-10-28 11:43:10', 'model': 'Truck', 'type': 'TPMS', 'id': '50000c66', 'wheel': 239, 'pressure_kPa': 0, 'temperature_C': 0, 'state': 1, 'flags': 10, 'mic': 'CHECKSUM'}
Truck.ID50000c66 Temperature: 0°C
{'time': '2024-10-28 11:43:12', 'model': 'Renault', 'type': 'TPMS', 'id': 'fa6ec6', 'flags': '05', 'pressure_kPa': 192.0, 'temperature_C': -30, 'mic': 'CRC'}
Renault.IDfa6ec6 Temperature: -30°C
{'time': '2024-10-28 11:43:12', 'model': 'Renault', 'type': 'TPMS', 'id': 'fa6ec6', 'flags': '05', 'pressure_kPa': 192.0, 'temperature_C': -30, 'mic': 'CRC'}
Renault.IDfa6ec6 Temperature: -30°C
{'time': '2024-10-28 11:43:13', 'model': 'Abarth-124Spider', 'type': 'TPMS', 'id': '150000c6', 'flags': '6e', 'pressure_kPa': 345.0, 'temperature_C': -48, 'status': 64, 'mic': 'CHECKSUM'}
Abarth-124Spider.ID150000c6 Temperature: -48°C
{'time': '2024-10-28 11:43:13', 'model': 'Abarth-124Spider', 'type': 'TPMS', 'id': '150000c6', 'flags': '6e', 'pressure_kPa': 345.0, 'temperature_C': -48, 'status': 64, 'mic': 'CHECKSUM'}
Abarth-124Spider.ID150000c6 Temperature: -48°C
{'time': '2024-10-28 11:43:32', 'model': 'Renault', 'type': 'TPMS', 'id': 'fa6ec6', 'flags': '05', 'pressure_kPa': 192.0, 'temperature_C': -30, 'mic': 'CRC'}
Renault.IDfa6ec6 Temperature: -30°C
{'time': '2024-10-28 11:43:32', 'model': 'Renault', 'type': 'TPMS', 'id': 'fa6ec6', 'flags': '05', 'pressure_kPa': 192.0, 'temperature_C': -30, 'mic': 'CRC'}
Renault.IDfa6ec6 Temperature: -30°C
{'time': '2024-10-28 11:43:35', 'model': 'Truck', 'type': 'TPMS', 'id': '50000c66', 'wheel': 239, 'pressure_kPa': 0, 'temperature_C': 0, 'state': 1, 'flags': 10, 'mic': 'CHECKSUM'}
Truck.ID50000c66 Temperature: 0°C
{'time': '2024-10-28 11:43:35', 'model': 'Truck', 'type': 'TPMS', 'id': '50000c66', 'wheel': 239, 'pressure_kPa': 0, 'temperature_C': 0, 'state': 1, 'flags': 10, 'mic': 'CHECKSUM'}
Truck.ID50000c66 Temperature: 0°C
{'time': '2024-10-28 11:43:36', 'model': 'Truck', 'type': 'TPMS', 'id': '50000c66', 'wheel': 239, 'pressure_kPa': 0, 'temperature_C': 0, 'state': 1, 'flags': 10, 'mic': 'CHECKSUM'}
Truck.ID50000c66 Temperature: 0°C
{'time': '2024-10-28 11:43:36', 'model': 'Truck', 'type': 'TPMS', 'id': '50000c66', 'wheel': 239, 'pressure_kPa': 0, 'temperature_C': 0, 'state': 1, 'flags': 10, 'mic': 'CHECKSUM'}
Truck.ID50000c66 Temperature: 0°C
{'time': '2024-10-28 11:43:36', 'model': 'Truck', 'type': 'TPMS', 'id': '50000c66', 'wheel': 239, 'pressure_kPa': 0, 'temperature_C': 0, 'state': 1, 'flags': 10, 'mic': 'CHECKSUM'}
Truck.ID50000c66 Temperature: 0°C
{'time': '2024-10-28 11:43:36', 'model': 'Truck', 'type': 'TPMS', 'id': '50000c66', 'wheel': 239, 'pressure_kPa': 0, 'temperature_C': 0, 'state': 1, 'flags': 10, 'mic': 'CHECKSUM'}
Truck.ID50000c66 Temperature: 0°C
```

### Conclusion

This `RTLSDR` module is an efficient way to expand the Modular Biped project with IoT connectivity using a Nooelec SDR USB dongle. By harnessing the 433 MHz frequency, this module enables real-time data publication over PubSub, allowing other modules to interact with and respond to nearby IoT devices seamlessly.