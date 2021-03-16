Raspberry Pi Zero W - Bluetooth Working

Intro
=====

These instructions apply to the raspberry pi zero w model of the Raspberry Pi:


How to build it
===============

Configure Buildroot
-------------------

There is one defconfig file in Buildroot, which you should base your work on:

  $ make pi0wbluetooth_defconfig


Build the rootfs
----------------

Note: you will need to have access to the network, since Buildroot will
download the packages' sources.

You may now build your rootfs with:

  $ make

(This may take a while, consider getting yourself a coffee ;-) )

Result of the build
-------------------

After building, you should obtain this tree:

    output/images/
    +-- bcm2708-rpi-zero-w.dtb
    +-- boot.vfat
    +-- rootfs.ext2
    +-- rootfs.ext4
    +-- rpi-firmware/
    |   +-- bootcode.bin
    |   +-- cmdline.txt
    |   +-- config.txt
    |   +-- fixup.dat
    |   +-- start.elf
    |   `-- overlays/
    +-- sdcard.img
    +-- zImage

How to write the SD card
========================

Once the build process is finished you will have an image called "sdcard.img"
in the output/images/ directory.

Copy the bootable "sdcard.img" onto an SD card with "dd":

  $ sudo dd if=output/images/sdcard.img of=/dev/sdX

Insert the SDcard into your Raspberry Pi, and power it up. Your new system
should come up now. Note that no console is present - only access is via SSH.

