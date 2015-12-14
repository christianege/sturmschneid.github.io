---
layout: post
title: "OpenEmbedded support for the upcomming UDOO Neo"
date: 2015-09-19 19:18:03 +0200
comments: true
categories: [Yocto, Linux, Open Embedded,UDOO, UDOO NEO]
---

After beeing part of the [Kickstarter campaign](https://www.kickstarter.com/projects/udoo/udoo-neo-raspberry-pi-arduino-wi-fi-bt-40-sensors/?src=i1) for the [UDOO NEO board](http://www.udoo.org/udoo-neo/). I am looking forward to play with this little beast.

{% img center http://cdn.udoo.org/wp-content/uploads/2015/03/UDOO_neo_up2_ks_funded.png %}

During the meantime I decided to add the [UDOO NEO board](http://www.udoo.org/udoo-neo/) to the [Freescale OpenEmbedded community layer](https://github.com/Freescale/meta-fsl-arm-extra). Due to the fact my board did not yet arrived I've not yet created a patch to ask for integration into the official [Freescale OpenEmbedded community layer](https://github.com/Freescale/meta-fsl-arm-extra). This will be done when I've tested the layer.

If you are interested in my work you can find my personal fork right [here at github](https://github.com/graugans/meta-fsl-arm-extra/).
You have to use the [fido](https://github.com/graugans/meta-fsl-arm-extra/tree/fido) branch. To keep things simple I've created my own
branch of the [fsl-community-bsp-platform](https://github.com/graugans/fsl-community-bsp-platform) repo repository.


# Installing the BSP

To get the BSP you need to have repo installed and use it as:

Install the repo utility:

    $: mkdir ~/bin
    $: curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    $: chmod a+x ~/bin/repo

Download the BSP source:

    $: PATH=${PATH}:~/bin
    $: mkdir fsl-community-bsp
    $: cd fsl-community-bsp
    $: repo init -u https://github.com/graugans/fsl-community-bsp-platform -b fido
    $: repo sync

At the end of the commands you have every metadata you need to start work with.


# Building the Image

To start a base image build execute the following command on a decent machine and grab some coffee...

$: source ./setup-environment udooneo-build
$: bitbake core-image-base


If everything was build fine you'll find a SD-Card image right here:

    tmp/deploy/images/udooneo/core-image-base-udooneo.sdcard

You can write this image directly to any supported SD-Card. Plug it into your freshly unboxed UDOO NEO and hopefully have fun. This image is completly untested and you have to use it on your own risk...

# Creating your personal Toolchain

If you want to develop your own software for the UDOO Neo you'll need some cross toolchain. This can be achieved very easy with the OpenEmbedded universe.

    $ bitbake core-image-base -cpopulate_sdk

The resulting self extracting shell script you'll find right here:

    tmp/deploy/sdk/poky-glibc-x86_64-core-image-base-cortexa9hf-vfp-neon-toolchain-1.8.sh

In case of any questions just leave a comment on my Blog.