---
layout: post
title: "Attaching a serial console to UDOO Neo"
date: 2015-10-31 16:56:22 +0100
comments: true
categories: [UDOO, UDOO Neo, Yocto, UDOOBuntu]
---

Yesterday my long time awaited [UDOO Neo](http://www.udoo.org/udoo-neo/) board has arrived. They do provide a really good [getting started guide](http://www.udoo.org/get-started-neo/) for UDOO Neo. But for my Yocto Port I need access to the serial console to check the output of the U-Boot and the kernel. So I've checked the [UDOO Neo Schematics](http://udoo.org/download/files/schematics/UDOO_NEO_schematics.pdf) and located UART1 on the connector J7.

{%img /images/posts/UDOOUartNeo.png %}

It is so incredible that they do provide the schematics. It is much easier to check the PINOUT. To connect the UDOO to a terminal client you need the following stuff

- 1 x USB to serial converter with 3.3V output. Do not use any 5V type, this may damage your UDOO Neo! I use some cheap Chinese [CH341](https://hackaday.com/tag/ch341) USB to UART converters. They cost about 3€ but are not FTDI clones.
- 3 Jumper wires. It depends on your USB to serial converter if you need male <-> male or female <-> male. I needed female <-> male connectors
- 1 x [UDOO Neo board](http://www.udoo.org/udoo-neo/)
- 1 x Powersupply for the Neo.
- 1 x Micro SD-Card with the UDOOBuntu or a [Yocto build](http://ch.ege.io/blog/2015/09/19/openembedded-support-for-the-upcomming-udoo-neo/) for UDOO Neo

For power connection and flashing the SD-Card please have a look at the [getting started guide](http://www.udoo.org/get-started-neo/) for UDOO Neo.

Before you plug-in the serial wires you have to remove any power source from the UDOO board. Otherwise you may damage your board. Double check if your adapter is a 3.3V. Connect the ground line (GND) of your USB to serial converter with the PIN 11 of the J7 connector. Then you have to connect the RX and TX PINs. But remember you have to cross them. Why do we need to cross RX and TX?

This is because ``TX`` is transmit and if the UDOO Board and your PC are transmitting on the same line nothing is received on both sides. So you have to connect the ``RX`` PIN of the UDOO board, PIN 4 of J7, with the ``TX`` PIN of your converter. The ``TX``, PIN 2 of J7, from UDOO is connected to the ``RX`` PIN of converter.

{%img /images/posts/UDOONeoAndUSBSeriell.png %}

After this you can check [their tutorial how to fire up an Terminal Emulator](http://www.udoo.org/tutorial/connecting-via-serial-cable/) on your Platform of choice and connect to the NEO at the following settings:

- 115200 kB
- 8N1
- No Flow-Control

I do prefer minicom under Linux for this job.

# UDOO Neo direct USB console

You may have noticed while booting the official [UDOOBuntu image](http://www.udoo.org/get-started-neo/) image a new disk is suddenly mounted on your PC. And you have a new network interface connected as well. Last but not least even a new serial interface is available.

    [...]
    [ 4885.463264] cdc_acm 4-1.4.1.1:1.2: ttyACM0: USB ACM device
    [ 4885.463682] usbcore: registered new interface driver cdc_acm
    [ 4885.463685] cdc_acm: USB Abstract Control Model driver for USB modems and ISDN adapters
    [...]

You can use minicom with ``/dev/ttyACM0`` and the same settings like above.

    Ubuntu 14.04.3 LTS udooneo ttyGS0


    default username:password is [udooer:udooer]
    udooneo login:

This is absolutely awesome!


