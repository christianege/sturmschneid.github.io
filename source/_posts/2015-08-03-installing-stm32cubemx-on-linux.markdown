---
layout: post
title: "Installing STM32CubeMX on Linux"
date: 2015-08-03 06:11:16 +0200
comments: true
categories: [Linux, Ubuntu STM, STM32CubeMX]
---

The STMicroelectronics STM32CubeMX is delivered as a exe file what implies that it is eintended to run on Windows systems only. But due to the fact it is a JAVA application we can run it on Linux as-well.

# Installation

To install the STM32CubeMX administration rights are needed

    sudo java -jar /tmp/SetupSTM32CubeMX-4.9.0.exe

# Running STM32CubeMX

If you've selected the default install location you can run STM32CubeMX like this:

    java -jar /usr/local/STMicroelectronics/STM32Cube/STM32CubeMX/STM32CubeMX.exe

