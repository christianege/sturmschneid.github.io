---
layout: post
title: "Full Open Source DVB-S2 board"
date: 2015-07-04 19:43:00 +0200
comments: true
categories: [NIM, USB, DVB-S2, DVB, OSS, linux, SP2246, STV6111, STV0913 ]
---

Some time ago I thought it would be fun to build a USB DVB-S2 device which runs on 100% OpenSource Software. Due to the fact I do not have any tooling for soldering complex chips I decided to build this project on hardware which is already available. For interconnection of the DVB part with the PC or a Single board computer like the ODROID-C1 or Raspberry PI my decision was to use a [Cypress FX2 CY7C68013A](http://www.cypress.com/?id=193) evaluation board. You can buy them for under 10€ in far east on ebay. I bought one of the [Lcsoft Mini Boards](http://sigrok.org/wiki/Lcsoft_Mini_Board). The coreboot project provides a [schematic for the Lcsoft board](http://www.coreboot.org/File:Fx2lp_lcsoft_schematic_A.pdf).

{%  img http://sigrok.org/wimg/e/ec/Lcsoft-miniboard-front.png 'Lcsoft Mini Board CC-BY 3.0' %}

The [Cypress FX2 CY7C68013A](http://www.cypress.com/?id=193) is based on a 8051 core. Not one of my favorite hacking platform but I'll give it a try. There is a Open Source Framework for firmware development it is called [fxlib](https://github.com/djmuhlestein/fx2lib). After some research on Google I found the project [Termini](http://qanu.de/termini.php). The firmware for the Termini hardware is available via the linuxtv.org [CVS repository](http://linuxtv.org/cgi-bin/viewvc.cgi/dvb-hw/dvbusb-fx2/termini/). They provide a specification for the [USB communication](http://qanu.de/docs/dvbt-usb-spec.pdf) between the host controller and the FX2. There are already some USB 2.0 USB DVB-S2 devices based on the [Cypress FX2 CY7C68013A](http://www.cypress.com/?id=193) like the [DVBSKY S960](http://www.linuxtv.org/wiki/index.php/DVBSKY_S960).

The last part in the jigsaw puzzle is the DVB-S2 tuner and demodulator part. Some of the linux based Set-Top-Boxes provide swappable tuner modules one of them is the manufacturer GigaBlue. They sell a [DVB-S2 Tuner](http://www.gigablue.de/portfolio-item/gigablue-dvb-s2-tuner/) based on the Serit [SP2246 NIM for DVB-S2](http://serit.hk/Products_Details.aspx?IDX=144). The SP2246 is based on the [STV6111](http://www.st.com/web/catalog/mmc/FM131/SC1003/PF251478) tuner and the [STV0913](http://www.st.com/web/catalog/mmc/FM131/SC628/SS1334/PF252767?s_searchtype=partnumber) demodulator. It looks like the tuner module is connected via a [PCI-Express x1 36Pin](https://en.wikipedia.org/wiki/PCI_Express#Form_factors) connector. Serit provides a very detailed [data sheet](http://serit.hk/Manager/Public/pdf/201212070342307ead.pdf) so at least a few of the PINs should be easily mappable with a multi meter. 

{%img /images/posts/sp2246.jpg %}

