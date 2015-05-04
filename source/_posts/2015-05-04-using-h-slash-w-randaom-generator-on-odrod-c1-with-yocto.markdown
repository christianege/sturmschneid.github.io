---
layout: post
title: "Using H/W randaom generator on ODROD-C1 with Yocto"
date: 2015-05-04 05:30:18 +0200
comments: true
categories: [Yocto, ODROID-C1, PRNG, cryptography, linux ]
---

The Amlogic S805 CPU used in the ODROID-C1 contains a hardware random number generator which can be used to increase entropy in the linux kernel. The guys at Hardkernel wrote a [tutorial how to enable this in Ubuntu](http://odroid.com/dokuwiki/doku.php?id=en:c1_hardware_number_generator).

Due to the fact that there is a [rng-tools recipe](http://cgit.openembedded.org/meta-openembedded/tree/meta-oe/recipes-support/rng-tools/rng-tools_4.bb?h=fido) in the [meta-openembedded](http://cgit.openembedded.org/meta-openembedded/) repo I thought it would be a no-brainer to get this working in Yocto fido.

Before you can build the rng-tools you need the [meta-openembedded](http://cgit.openembedded.org/meta-openembedded/) meta-oe layer in your bblayer configuration file this may look like this

{% codeblock lang:diff %}
diff --git a/conf/bblayers.conf b/conf/bblayers.conf
index d999c9d..fcf7bf2 100644
--- a/conf/bblayers.conf
+++ b/conf/bblayers.conf
@@ -9,8 +9,12 @@ BBLAYERS ?= " \
   /build/hardkernel/odroid-c1/poky/meta \
   /build/hardkernel/odroid-c1/poky/meta-yocto \
   /build/hardkernel/odroid-c1/poky/meta-yocto-bsp \
+  /build/hardkernel/odroid-c1/meta-amlogic \
+  /build/hardkernel/odroid-c1/meta-openembedded/meta-oe \
   "
{% endcodeblock %}

After adding the meta-oe layer and preparing the bitbake environment we can build the rng-tools recipe

    $ source poky/oe-init-build-env build/
    $ bitbake rng-tools

The resulting file can be transfered to the target and installed with the defined package manger. In my case I do use opkg. Please ensure you are using a image with a package manager enabled. I do use core-image-base and add the following to conf/local.conf:
{% codeblock lang:diff %}
diff --git a/conf/local.conf b/conf/local.conf
index a1d99f9..3e200c9 100644
--- a/conf/local.conf
+++ b/conf/local.conf
@@ -104,13 +119,13 @@ DISTRO ?= "poky"
 #  - 'package_rpm' for rpm style packages
 # E.g.: PACKAGE_CLASSES ?= "package_rpm package_deb package_ipk"
 # We default to rpm:
-PACKAGE_CLASSES ?= "package_rpm"
+PACKAGE_CLASSES ?= "package_ipk"
@@ -137,13 +152,13 @@ PACKAGE_CLASSES ?= "package_rpm"
 # There are other application targets that can be used here too, see
 # meta/classes/image.bbclass and meta/classes/core-image.bbclass for more details.
 # We default to enabling the debugging tweaks.
-EXTRA_IMAGE_FEATURES = "debug-tweaks"
+EXTRA_IMAGE_FEATURES = "debug-tweaks package-management ssh-server-dropbear"
{% endcodeblock %}

Let's transfer the resulting files to the target.

    $ find tmp/deploy/ipk/ -name "rng-tools*"
    $ scp tmp/deploy/ipk/cortexa5hf-vfp-neon/rng-tools_4-r0_cortexa5hf-vfp-neon.ipk root@odroidc1:/tmp

**Attention**: At the moment the recipe for the rng-tools packages installs it's configuration to */etc/rng-tools* but the initscript expects it at */etc/default/rng-tools*. I send a [patch](http://article.gmane.org/gmane.comp.handhelds.openembedded/68813) to the meta-openembedded mailinglist to correct this. The current behaviour of the rng-tools should be pretty okay due to the fact that given no "-r" option the default behavior is to use /dev/hwrng.


## Benchmarks

The rng-tools provide some statistical tests for checking if the hardware random generator works as expected.

### Without rngd

This runs the [FIPS 140-2 tests](http://en.wikipedia.org/wiki/FIPS_140-2) against the device */dev/random* **without** running rngd

    root@odroidc1:~# time rngtest -c1 < /dev/random
    rngtest 4
    Copyright (c) 2004 by Henrique de Moraes Holschuh
    This is free software; see the source for copying conditions.  There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

    rngtest: starting FIPS tests...
    rngtest: bits received from input: 20032
    rngtest: FIPS 140-2 successes: 1
    rngtest: FIPS 140-2 failures: 0
    rngtest: FIPS 140-2(2001-10-10) Monobit: 0
    rngtest: FIPS 140-2(2001-10-10) Poker: 0
    rngtest: FIPS 140-2(2001-10-10) Runs: 0
    rngtest: FIPS 140-2(2001-10-10) Long run: 0
    rngtest: FIPS 140-2(2001-10-10) Continuous run: 0
    rngtest: input channel speed: (min=132.975; avg=132.975; max=132.975)bits/s
    rngtest: FIPS tests speed: (min=55.934; avg=55.934; max=55.934)Mibits/s
    rngtest: Program run time: 150404986 microseconds
    real    2m 30.40s
    user    0m 0.00s
    sys     0m 0.01s

The average of **132.975 bits/s** throughput is pretty low.

### With rngd

This runs the [FIPS 140-2 tests](http://en.wikipedia.org/wiki/FIPS_140-2) against the device */dev/random* **with** running rngd

    root@odroidc1:~# /etc/init.d/rng-tools start
    root@odroidc1:~# time rngtest -c1 < /dev/random
    root@odroidc1:~# time rngtest -c100 < /dev/random
    rngtest 4
    Copyright (c) 2004 by Henrique de Moraes Holschuh
    This is free software; see the source for copying conditions.  There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

    rngtest: starting FIPS tests...
    rngtest: bits received from input: 2000032
    rngtest: FIPS 140-2 successes: 99
    rngtest: FIPS 140-2 failures: 1
    rngtest: FIPS 140-2(2001-10-10) Monobit: 1
    rngtest: FIPS 140-2(2001-10-10) Poker: 0
    rngtest: FIPS 140-2(2001-10-10) Runs: 0
    rngtest: FIPS 140-2(2001-10-10) Long run: 0
    rngtest: FIPS 140-2(2001-10-10) Continuous run: 0
    rngtest: input channel speed: (min=5.034; avg=5.310; max=5.374)Mibits/s
    rngtest: FIPS tests speed: (min=53.880; avg=58.279; max=58.688)Mibits/s
    rngtest: Program run time: 392465 microseconds
    Command exited with non-zero status 1
    real    0m 0.39s
    user    0m 0.03s
    sys     0m 0.36s

Now we have an average throughput of **5.310 Mibits/s** compared to the **132.975 bits/s** without rngd.


## Considerations

You might keep in mind that using a hardware random generator may cause security issues. To have a more detailed view on this read this posting and comments on [Theodore Ts'o posting on G+](https://plus.google.com/+TheodoreTso/posts/SDcoemc9V3J). From this posting *If I were the NSA, and I wanted to gimmick an RNG, whether it be the one inside the Intel chip or the Android build, it would be a counter encrypted by an AES key known by the NSA.  (Why AES?   Because RDRAND is documented as using AES in its final whitening stage; so someone taking a quick look at the RDRAND implementation would be expecting AES).*

