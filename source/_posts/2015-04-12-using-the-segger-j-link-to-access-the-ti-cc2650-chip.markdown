---
layout: post
title: "Using the SEGGER J-Link to access the TI CC2650 Chip"
date: 2015-04-12 17:07:32 +0200
comments: true
categories: [SEGGER, J-Link, CC2650, CC2650F128, SensorTag, Linux, SimpleLink]
---

Before you get started install the [newest host tools](https://www.segger.com/jlink-software.html) for your SEGGER in my case I installed the Debian package of version **V4.98b**. On first start the **JLinExe** installs the apropriate Firmware.

    $ JLinkExe -if jtag -device CC2650F128
    SEGGER J-Link Commander V4.98b ('?' for help)
    Compiled Apr 10 2015 20:27:38
    Updating firmware:  J-Link V9 compiled Apr 10 2015 10:51:08
    Replacing firmware: J-Link V9 compiled Feb 13 2015 20:37:28
    Waiting for new firmware to boot
    New firmware booted successfully

On the [SimpleLink™ Bluetooth Smart®/Multi-Standard SensorTag](http://www.ti.com/tool/cc2650stk) they use the [CC2650](http://www.ti.com/product/cc2650) with 128kB of Flash ROM. So you have to select *-device CC2650F128* when running **JLinkExe** At the moment it seems the [CC2650](http://www.ti.com/product/cc2650) only supports JTAG debugging and not the more common SWI debug like STM32 do use. Anyway The Segger can run both modes.

After a succesful firmawre update the communication looks like this

    SEGGER J-Link Commander V4.98b ('?' for help)
    Compiled Apr 10 2015 20:27:38
    Info: Device "CC2650F128" selected.
    DLL version V4.98b, compiled Apr 10 2015 20:27:35
    Firmware: J-Link V9 compiled Apr 10 2015 10:51:08
    Hardware: V9.30
    S/N: XXXXXXXX
    OEM: SEGGER-EDU
    Feature(s): FlashBP, GDB
    VTarget = 2.800V
    Info: TotalIRLen = 10, IRPrint = 0x0011
    Info: Found Cortex-M3 r2p1, Little endian.
    Info: FPUnit: 6 code (BP) slots and 2 literal slots
    Info: CoreSight components:
    Info: ROMTbl 0 @ E00FF000
    Info: ROMTbl 0 [0]: FFF0F000, CID: B105E00D, PID: 000BB000 SCS
    Info: ROMTbl 0 [1]: FFF02000, CID: B105E00D, PID: 003BB002 DWT
    Info: ROMTbl 0 [2]: FFF03000, CID: B105E00D, PID: 002BB003 FPB
    Info: ROMTbl 0 [3]: FFF01000, CID: B105E00D, PID: 003BB001 ITM
    Info: ROMTbl 0 [4]: FFF41000, CID: B105900D, PID: 003BB923 TPIU-Lite
    Found 2 JTAG devices, Total IRLen = 10:
     #0 Id: 0x4BA00477, IRLen: 04, IRPrint: 0x1, CoreSight JTAG-DP (ARM)
     #1 Id: 0x8B99A02F, IRLen: 06, IRPrint: 0x1, TI ICEPick
    Cortex-M3 identified.
    Target interface speed: 100 kHz
    J-Link>

The next step is to create a firmware backup of the original firmware. This is needed because this little beast at the momemnt denies to build its firmware under Linux. The CodeComposerStudio Projekt is using some Windows Tools to build and expects a specific directory layout.

