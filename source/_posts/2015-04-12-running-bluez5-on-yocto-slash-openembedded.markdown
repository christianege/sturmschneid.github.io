---
layout: post
title: "Running BlueZ5 on Yocto/OpenEmbedded"
date: 2015-04-12 07:09:49 +0200
comments: true
sharing: true
categories: [BlueZ5, BlueZ, Yocto]
---

At the moment yocto/openembedded ships with [BlueZ4](http://www.bluez.org) as the Linux Bluetooth protocol stack. Due to the fact that [Bluetooth low energy](http://en.wikipedia.org/wiki/Bluetooth_low_energy) is only supported in BlueZ5 this is a bit of a disappointment. There is a long-standing bug report on this [#5031 ](https://bugzilla.yoctoproject.org/show_bug.cgi?id=5031). But since the commit [1139cc4e...](http://git.yoctoproject.org/cgit/cgit.cgi/poky/commit/?id=1139cc4eef305fc14bc5db19a5f8729e7b3bf27a) the used BlueZ version can be selected. According to the reference manual BlueZ5 can be enabled by adding bluez5 to the [DISTRO_FEATURES](http://www.yoctoproject.org/docs/1.8/ref-manual/ref-manual.html#var-DISTRO_FEATURES) for me the easiest way to to this was adding the following line to my conf/local.conf in the [Build Directory](http://www.yoctoproject.org/docs/1.8/dev-manual/dev-manual.html#build-directory).

    DISTRO_FEATURES = "ext2 alsa wifi usbhost bluetooth bluez5 ${DISTRO_FEATURES_LIBC}"

This disables x11 for example. But you may also could add something like this:

    DISTRO_FEATURES_append = "bluez5"

After this run an build of your preferred image. In my case this was "core-image-base"

    bitbake core-image-base

To have full bluetooth support on the target I needed to install some extra packages. To get a list of all bluetooth related packages run the following command. For this you need a [opkg/ipkg feed](http://www.yoctoproject.org/docs/1.8/ref-manual/ref-manual.html#package-feeds-dev-environment) server.

    root@odroidc1:~# opkg list | grep blue
    bluez5 - 5.29-r0 - Linux Bluetooth Stack Userland V5  Linux Bluetooth stack V5 userland
    bluez5-dbg - 5.29-r0 - Linux Bluetooth Stack Userland V5 - Debugging files  Linux Bluetooth
    bluez5-dev - 5.29-r0 - Linux Bluetooth Stack Userland V5 - Development files  Linux Bluetooth
    bluez5-doc - 5.29-r0 - Linux Bluetooth Stack Userland V5 - Documentation files  Linux Bluetooth
    bluez5-noinst-tools - 5.29-r0 - Linux Bluetooth Stack Userland V5  Linux Bluetooth stack V5 userland
    bluez5-obex - 5.29-r0 - Linux Bluetooth Stack Userland V5  Linux Bluetooth stack V5 userland
    bluez5-testtools - 5.29-r0 - Linux Bluetooth Stack Userland V5  Linux Bluetooth stack V5 userland
    libasound-module-bluez - 5.29-r0 - Linux Bluetooth Stack Userland V5  Linux Bluetooth stack V5 userland
    packagegroup-base-bluetooth - 1.0-r83 - Bluetooth support  Merge machine and distro options to create a basic

I installed the following packages:

    bluez5-testtools, bluez5-noinst-tools

The later **bluez5-noinst-tools** was required for the gatttool.
