---
layout: post
title: "Activating USB_OTG Composite Ethernet + Serial Gadget support in Yocto's Poky for UDOO Neo"
date: 2015-11-01 09:23:35 +0100
comments: true
categories: [Yocto, UDOO, UDOO Neo, Poky, Ethernet, Serial,Gadget]
---

After playing with UDOOBuntu and the nice USB OTG Gadget support my decision was to provide something similar for my Yocot Port as-well. It turned out that the serial Gadget support was pretty easy the Ethernet support is more complex.

For serial console over the USB OTG port of your NEO you have to change the ``udooneo.conf`` file in the folder ``conf\machine`` of ``meta-fsl-arm-extra``


    MACHINE_FEATURES += " usbgadget usbhost"
    SERIAL_CONSOLES ?= "115200;ttymxc0 115200;ttyGS0"

    KERNEL_MODULE_AUTOLOAD += " g_serial"

This adds usbgadget support and automatically loads the module g_serial on startup. The line ``SERIAL_CONSOLES`` automatically spawns a [getty](https://en.wikipedia.org/wiki/Getty_%28Unix%29) on  ``/dev/ttyGS0`` through the [inittab](https://en.wikipedia.org/wiki/Init#SysV-style).

Due to the fact that only one USB OTG gagdget module can be loaded there are some composite modules available. A combination of serial and Ethernet for example is the  ``g_cdc``. To use ``g_cdc`` instead of ``g_serial`` is pretty simple just change the line

    KERNEL_MODULE_AUTOLOAD += " g_serial"

To this

    KERNEL_MODULE_AUTOLOAD += " g_cdc"

This will automatically provide serial support and spawns a ``usb0`` network device device on the target and the host. But my Ubuntu host was not able to set get an IP-Adress for this interface. Setting one by hand resolved this. But this is not comparable to the out of the box experience UDOOBuntu provides.

On your host computer you'll see something like this in your dmesg log

    [ 9515.050081] usb 4-1.4.1.1: new high-speed USB device number 12 using ehci-pci
    [ 9515.144407] usb 4-1.4.1.1: New USB device found, idVendor=0525, idProduct=a4aa
    [ 9515.144411] usb 4-1.4.1.1: New USB device strings: Mfr=1, Product=2, SerialNumber=0
    [ 9515.144412] usb 4-1.4.1.1: Product: CDC Composite Gadget
    [ 9515.144414] usb 4-1.4.1.1: Manufacturer: Linux 3.14.28_1.0.0_ga-udooboard+g580e305 with 2184000.usb
    [ 9515.152930] cdc_ether 4-1.4.1.1:1.0 usb0: register 'cdc_ether' at usb-0000:00:1d.0-1.4.1.1, CDC Ethernet Device, 76:26:58:xx:xx:xx
    [ 9515.153714] cdc_acm 4-1.4.1.1:1.2: ttyACM0: USB ACM device
    [ 9515.176120] cdc_ether 4-1.4.1.1:1.0 usb0: kevent 12 may have been dropped
    [ 9515.193036] IPv6: ADDRCONF(NETDEV_UP): usb0: link is not ready
    [ 9515.377616] userif-3: sent link down event.
    [ 9515.377619] userif-3: sent link up event.

For serial console access you can connet to the UDOO Neo by attaching minicom to ``/dev/ttyACM0`` at 115200 Baud 8N1 No flow control, or any other treminal emulator of your choice.
