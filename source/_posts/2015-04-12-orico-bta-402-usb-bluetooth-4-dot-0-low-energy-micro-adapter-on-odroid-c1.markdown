---
layout: post
title: "ORICO BTA-402 USB Bluetooth 4.0 Low Energy Micro Adapter on ODROID-C1"
date: 2015-04-12 09:11:53 +0200
comments: true
categories: [yocto,ODROID C1,BLE Bluetooth Low Energy]
---
Today I had some fun with [Bluetooth Low Energy](http://en.wikipedia.org/wiki/Bluetooth_low_energy) and my [ODROID C1](http://www.hardkernel.com/main/products/prdt_info.php) In an earlier [Blog Post](/blog/2015/04/12/running-bluez5-on-yocto-slash-openembedded/) I described how to enable Bluetooth Low Energy Support in Yocto.

Recently I bought this [ORICO BTA-402 USB Bluetooth 4.0 Low Energy Micro Adapter](http://www.amazon.de/gp/product/B00K5TJP02?psc=1&redirect=true&ref_=oh_aui_detailpage_o04_s00) on Amazon to play with Bluetooth Low Energy. The USB Dongle was automatically detected by my [meta-amlogic's](https://github.com/project-magpie/meta-amlogic) kernel configuration for the [ODROID C1](http://www.hardkernel.com/main/products/prdt_info.php)

I used the New [SimpleLink™ Bluetooth Smart®/Multi-Standard SensorTag](http://www.ti.com/tool/cc2650stk) as the device to connect to.

{% img /images/SensorTag2.png %}

On the ODROID-C1 device run the following commands:

    root@odroidc1:~# hciconfig
    hci0:   Type: BR/EDR  Bus: USB
        BD Address: 00:1A:7D:DA:71:09  ACL MTU: 310:10  SCO MTU: 64:8
        DOWN
        RX bytes:547 acl:0 sco:0 events:27 errors:0
        TX bytes:384 acl:0 sco:0 commands:27 errors:0

Check if your adapter is DOWN as you can see in the example above. If the adapter is down you have to bring it up befare any other action can take place

    hciconfig device up

After this you can re-check if your adapter is UP and RUNNING

    root@odroidc1:~# hciconfig
    hci0:   Type: BR/EDR  Bus: USB
            BD Address: 00:1A:7D:DA:71:09  ACL MTU: 310:10  SCO MTU: 64:8
            UP RUNNING
            RX bytes:10116 acl:72 sco:0 events:364 errors:0
            TX bytes:1858 acl:73 sco:0 commands:66 errors:0

Now you can scan for Bluetooth Low Energy devices Please ensure that your SensorTag is in Advertise Mode. Whith the default Firmware this can be achieved by pressing the Power Button.

    root@odroidc1:~# hcitool lescan
    LE Scan ...
    50:C4:7F:6C:18:2C (unknown)
    50:C4:7F:6C:18:2C (unknown)
    50:C4:7F:6C:18:2C (unknown)
    50:C4:7F:6C:18:2C (unknown)
    50:C4:7F:6C:18:2C (unknown)
    68:C9:0B:06:EF:0B (unknown)
    68:C9:0B:06:EF:0B CC2650 SensorTag
    68:C9:0B:06:EF:0B (unknown)
    68:C9:0B:06:EF:0B CC2650 SensorTag
    68:C9:0B:06:EF:0B (unknown)
    68:C9:0B:06:EF:0B CC2650 SensorTag
    68:C9:0B:06:EF:0B (unknown)
    68:C9:0B:06:EF:0B CC2650 SensorTag
    50:C4:7F:6C:18:2C (unknown)

After a successful scan we can query some information from the SensorTag

    root@odroidc1:~# hcitool leinfo 68:C9:0B:06:EF:0B
    Requesting information ...
            Handle: 71 (0x0047)
            LMP Version: 4.1 (0x7) LMP Subversion: 0x200
            Manufacturer: Texas Instruments Inc. (13)
            Features: 0x01 0x00 0x00 0x00 0x00 0x00 0x00 0x00

Now lets connect to the SensorTag

    root@odroidc1:~# hcitool lecc 68:C9:0B:06:EF:0B
    Connection handle 71

After this we can use the gatttool from the BlueZ5 package to connect to the GATT Server on the SensorTag

    root@odroidc1:~# gatttool -b 68:C9:0B:06:EF:0B --interactive
    [68:C9:0B:06:EF:0B][LE]>

Establish a connection to the GATT Server

     [68:C9:0B:06:EF:0B][LE]> connect
     Attempting to connect to 68:C9:0B:06:EF:0B
     Connection successful

Scan for Primary UUIDs

    [68:C9:0B:06:EF:0B][LE]> primary
    attr handle: 0x0001, end grp handle: 0x0007 uuid: 00001800-0000-1000-8000-00805f9b34fb
    attr handle: 0x0008, end grp handle: 0x000b uuid: 00001801-0000-1000-8000-00805f9b34fb
    attr handle: 0x000c, end grp handle: 0x001e uuid: 0000180a-0000-1000-8000-00805f9b34fb
    attr handle: 0x001f, end grp handle: 0x0026 uuid: f000aa00-0451-4000-b000-000000000000
    attr handle: 0x0027, end grp handle: 0x002e uuid: f000aa20-0451-4000-b000-000000000000
    attr handle: 0x002f, end grp handle: 0x0036 uuid: f000aa40-0451-4000-b000-000000000000
    attr handle: 0x0037, end grp handle: 0x003e uuid: f000aa80-0451-4000-b000-000000000000
    attr handle: 0x003f, end grp handle: 0x0046 uuid: f000aa70-0451-4000-b000-000000000000
    attr handle: 0x0047, end grp handle: 0x004b uuid: 0000ffe0-0000-1000-8000-00805f9b34fb
    attr handle: 0x004c, end grp handle: 0x0050 uuid: f000aa64-0451-4000-b000-000000000000
    attr handle: 0x0051, end grp handle: 0x0058 uuid: f000ac00-0451-4000-b000-000000000000
    attr handle: 0x0059, end grp handle: 0x0060 uuid: f000ccc0-0451-4000-b000-000000000000
    attr handle: 0x0061, end grp handle: 0xffff uuid: f000ffc0-0451-4000-b000-000000000000

To receive notifications on Key Press Events send the following to the device

    [68:C9:0B:06:EF:0B][LE]> char-write-req 4a 0100
    Notification handle = 0x0049 value: 01
    Notification handle = 0x0049 value: 00
    Notification handle = 0x0049 value: 01
    Notification handle = 0x0049 value: 00
    Notification handle = 0x0049 value: 01
    Notification handle = 0x0049 value: 00
    Notification handle = 0x0049 value: 01
    Notification handle = 0x0049 value: 00

Now each key press and release should be displayed in the gatttool. Frankly this key press event notification is just copy past from this [vine](https://vine.co/v/OVbOXVwBeYu) posting.

<iframe src="https://vine.co/v/OVbOXVwBeYu/embed/simple" width="600" height="600" frameborder="0"></iframe><script src="https://platform.vine.co/static/scripts/embed.js"></script>