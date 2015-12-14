---
layout: post
title: "Watterott HDMI/DVI - RGB Adapter EDID hacks"
date: 2015-06-15 08:09:57 +0200
comments: true
categories: [Watterott, HDMI, CEC, Display, Bus Pirate, EDID]
---

The German Maker supplier [Watterott](http://www.watterott.com) sells a [HDMI/DVI to RGB Display adapter](http://www.watterott.com/en/HDMI-RGB-adapter-with-ATmega32U4-touch-controller). The Adapter itself contains a ATmega32U4 controller attached to USB, which works as touchcontroller.  The designs and firmware for the adapter are available on [GitHub](https://github.com/watterott/HDMI-Display). I am not sure if we can call this OpenSource because of a missing License file.

{% img center https://raw.githubusercontent.com/watterott/HDMI-Display/master/hardware/HDMI-Display_v10dev.jpg %}

Beside the HDMI/DVI Bridge controller the board contains a EEPREOM to store the [EDID](https://en.wikipedia.org/wiki/Extended_Display_Identification_Data) data. EDID is needed to provide the attached Host device like your PC or Mac to identify what resolution and capabilities your Display has.

Due to the fact that the adapter supports a [wide range](https://github.com/watterott/HDMI-Display/blob/master/docu/Displays.md) of displays the EEPROM is not programmed on a freshly ordered board.

# Flashing the EDID EEPROM

Watterott suggests to flash the EEPROM through the ATmega32U4 controller and provides [a short description](https://github.com/watterott/HDMI-Display/blob/master/docu/FAQ.md#how-to-flashupdate-the-edid-eeprom) how to do this. But this requires to close two solder jumpers. I decided to flash the EDID EEPROM with my [Bus Pirate](http://dangerousprototypes.com/docs/Bus_Pirate) and the [Delock 65168](http://www.delock.de/produkte/G_65168/merkmale.html?setLanguage=en)

{% img center http://www.bilder.delock.de/produkte/orig/50f030782950b0.34465021.jpg %}

For me the [Delock 65168](http://www.delock.de/produkte/G_65168/merkmale.html?setLanguage=en) is the perfect toy to play around with EDID or [HDMI CEC](https://en.wikipedia.org/wiki/HDMI#CEC).

The host controller communicates through [I2C](https://en.wikipedia.org/wiki/I%C2%B2C) with the EDID EEPROM. There are at least 3 line needed to establish the [I2C](https://en.wikipedia.org/wiki/I%C2%B2C) communication. You need a good ground connection **(GND)** and a clock line **(SCL)** and of course the data line **(SDA)**
