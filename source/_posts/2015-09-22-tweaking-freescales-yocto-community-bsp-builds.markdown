---
layout: post
title: "Tweaking Freescales Yocto Community BSP Builds"
date: 2015-09-22 07:42:22 +0100
comments: true
categories: [UDOO, UDOO Neo, Yocto, directfb, freescale, i.mx6, ipk, linux, openembedded]
---

[Maurice le Rutte](https://twitter.com/scrumnl) asked me on Twitter if it is possible to add support for the first UDOO board and building a [DirectFB](https://en.wikipedia.org/wiki/DirectFB) enabled image. After some research it looked like a solution for the DirectFB task should be not that big issue.

I guess you have already a running Yocto environment based on the Freescale BSP comunity layers. To build a DirectFB image some tweaking is needed. You have to add those lines to your ``conf/local.conf``

    DISTRO_FEATURES_remove = "x11"
    DISTRO_FEATURES_remove = "wayland"
    DISTRO_FEATURES_append = " directfb"

The first two lines remove support for wayland and X11 which will conflict with DirectFB and will prevent a successful build. With this modification you can build a DirectFB image. The extra white space in front of directfb is mandatory!

    bitbake core-image-directfb

This is the right time to fetch some coffee.

Another tweak I prefer to do in the ``conf/local.conf`` is switching from RPM to IPK as package manager. It is more lightweight and a little bit faster than RPM or DEB. You only have to change the variable ``PACKAGE_CLASSES`` to ``package_ipk``

    PACKAGE_CLASSES ?= "package_ipk"

At the moment I am working on a Machine configuration for the [UDOO Quad board](http://www.udoo.org/udoo-dual-and-quad/)
