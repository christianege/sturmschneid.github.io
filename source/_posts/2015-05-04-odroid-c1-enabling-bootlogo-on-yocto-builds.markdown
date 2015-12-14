---
layout: post
title: "ODROID-C1 enabling bootlogo on Yocto builds"
date: 2015-05-04 11:40:53 +0200
comments: true
categories: [Yocto,ODROID-C1,bootlogo,Linux]
---

The U-Boot for the Amlogic S805 from [Hardkernel](https://github.com/hardkernel/u-boot/tree/odroidc-v2011.03) has the capabilities to display a bootlogo. It is not that easy to find any bootlogo that complies to a licence which can be used in comercial and in open source environments. Using the yocto logo is most probably prohibited, so I decided to design my own logo. And saved it to ** 24-bit Windows BMP image** with the dimension of **1280×720** as statet in the [ODROID-C1 Wiki](http://odroid.com/dokuwiki/doku.php?id=en:c1_tips#logo_file_format)

{% img https://raw.githubusercontent.com/linux-meson/meta-amlogic/fido/recipes-bsp/u-boot/u-boot-odroidc1/odroidc1/bootlogo.png %}

## Modifying boot.ini

In my Amlogic meta layer for the yocto build system I do not use a Fat32 boot partition so the [original bootlogo loading command]() needed some minor modifications. The bootlogo is name bootlogo.bmp and should be copied to /boot.

    # loading and displaying bootlogo
    logo size ${outputmode}
    video open
    video clear
    video dev open ${outputmode}
    ext4load mmc 0:1 ${loadaddr_logo} /boot/bootlogo.bmp
    bmp display ${loadaddr_logo}
    bmp scale

You can either add this to your boot.ini or wait until I pushed a patch for [meta-amlogic](https://github.com/linux-meson/meta-amlogic).

## Modifying the U-Boot environment

Another option is to change the U-Boot environment. There is already a variable called *preloadlogo*. We can update the command to load the bootlogo from ext4 instead of Fat32

    setenv preloadlogo 'logo size ${outputmode};video open;video clear;video dev open ${outputmode};ext4load mmc 0:1 ${loadaddr_logo} /boot/bootlogo.bmp;bmp display ${loadaddr_logo};bmp scale'

Pleas ensure, that you paste this as a single line.
