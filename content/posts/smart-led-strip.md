+++
title = "Making a smart LED Strip"
date = "2021-10-18"
author = "Mark"
cover = "/img/leds.jpg"
tags = ["milight", "esp8266", "led", "diy", "smart", "automation"]
keywords = ["milight", "esp8266", "led", "diy", "smart", "automation"]
description = "DIY smart LED Strip using NodeMCU ESP8266"
showFullContent = false
draft = true
+++



# Intro

This is a desk. It's cool, but lacks lights. Let's fix that.


# The Shopping List

    | Item                           | Cost |
    | ------------------------------ | ---- |
    | ESP8266 NodeMCU CP2102         | £3   |
    | Micro USB 5V Breakout Converter| £2   |
    | Dupont Female to Female Wire   | £5   |
    | 1m long WS2812B 60 LEDs        | £15  |


A Nodemcu on a desk:
![nodemcu](/img/nodemcuv2.jpeg)

# Plugging it all in

As it's 5 volts and a very short run we can wire this bad boy directly up.

Do a dry fit to make sure it all works, then break out the soldering iron.

# Prepare for installation

I'm using a mac with homebrew, so the below steps assume familarity with the terminal
and homebrew

```bash
brew install esptool
```


# Install the project

Download the latest release from the Github releases page.

Ensure your esp2866 is plugged into your computer before running the next step.

In your terminal navigate to where the binary is and run the esptool to flash the firmware

```bash
cd ~/Downloads

esptool.py write_flash 0x0 ./WLED_0.13.0-b4_ESP8266.bin

```

A successful flash will look like something like the below:

![success](/img/smart-led-success.png)


# Adding to the WiFi network
* Using your smartphone or laptop look for a new wifi network called `WLED-AP` and connect to it using the default password `wled1234`

![wifi](/img/wifi.png)

* Navigate to the IP address `http://4.3.2.1` in a browser
* Fill-in the relevant wifi information for your network
* Hit save


# Using the REST API
The WLED project has [comprehensive documentation](https://kno.wled.ge/interfaces/mqtt/)
for their REST API

# Next Steps
* Design and print a box
* Integrate with Home Assistant
* Make a nice friendly android app to control my lights
