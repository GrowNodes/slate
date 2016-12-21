---
title: API Reference

language_tabs:
  - c++

toc_footers:
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# About
<aside class="notice">
Copyright (C) 2016 All rights reserved except where required by law.
</aside>
Runs on Grow Nodes ESP8266 MCUs. PlatformIO project.

# Usage

## 0. Connect hardware
See pin map in `constants.h`

## 1. Build and flash the firmware
PlatformIO might throw an error abount empty projects. Verify `homie-esp8266` dir is in `.piolibdeps` dir and ignore the error.

**Optional**
Modify `.piolibdeps\homie-esp8266\src\Homie\Boot\BootConfig.cpp` to have the correct MQTT server address or the MQTT coms will not work.

## 2. Factory Reset (optional)
Hold down the FLASH button on the Node MCU for 5 seconds or until the Node MCU onboard LED turns solid blue.

## 3. Provision WiFi
Conncet your phone to a `2.4 GHz` access point and launch the [Android app](https://play.google.com/store/apps/details?id=com.cmmakerclub.iot.esptouch&hl=en) to provision the device.

# Base System
## Time
Time is set to Unix Epoch on bootup.

If there is WiFi, the time is synced via NTP as UTC time.

## Network Status LED
Blue LED, `HOMIE_STATUS_PIN`, default `D0` (built in NodeMCU blue led)

LED Behavior | Meaning
---------- | -------
Solid | WiFi is ready to provision via ESP Touch
Slow Blink | Attempting to connect to WiFi
Fast Blink | Attempting to connect to MQTT server


# Grow Program

## Grow Errors
The system publishes grow errors over MQTT

## Notifications

Will be sent only if the WiFi and MQTT are connected. Relayed to app as push notifications.

* ✔ Notify over MQTT every 6 hours if air temp is above `AIR_TEMP_OVERHEAT`
    * Tell user to move setup to cooler place
* ✔ Notify over MQTT if mock water level sensor indicates low water
   * or notify user every 3 days to check of water level
* Notify user to check pH every day
* Notify user if grow node loses connection
* Notify user to add nutrients following Grow Chart
    * Water first (calculated based on # of plants), pH/EC before filling water?
* Notify user to change water and measure nutrients on 10th day

## Grow light
* ✔ Turns on and off at preset hour (actually seconds for development)
* ✔ If unconfigured, defualts to on at 6 AM and off at 6 PM. See default grow light definitions in `constants.h`
* ✔ Turns off if air temp is above `AIR_TEMP_OVERHEAT`
* (selling point?) Light on dimmer following realistic light cycles?

## Air pump
* ✔ Always on but manually controllable

## Vent Fan
* ✔ Always on but manually controllable
* (Speed control) If temp > temp_max, change speed to full power until temp is 5 degrees before optimal

## Water pump
* ✔ Always on but manually controllable
    * FOR ONE UNIT: Water pump on 15 minutes, off for 15 minutes
* Turn off if water level is too low and the pump will run dry

## Sensors
* Communicate with Arduino over I2C
* Water temp sensor
* Air temp sensor
* pH sensor
* EC sensor
* Water Level sensor


# Structure
```c++
void setup {
  GrowProgram.setup();
  // etc...
}

void loop {
  GrowProgram.loop();
  // etc...
}
```
The system uses state machines and non-preemptive multitasking strategy.

<aside class="notice">
You must avoid using delay/blocking functions.
</aside>


# WiFi Configuration
Handled by ESP Touch / Smart Config and custom `homie-esp8266` library.

# MQTT
Handled by `homie-esp8266` library.

Server URL is hardcoded in `homie-esp8266\src\Homie\Boot\BootConfig.cpp`