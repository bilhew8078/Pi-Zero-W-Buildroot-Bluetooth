# Pi-Zero-W-Buildroot-Bluetooth
How I got Bluetooth working on a headless Raspberry Pi Zero W with a Buildroot OS

Disclaimer:  I spent weeks (more like months) trying to get Bluetooth working on a headless Raspberry Pi Zero W using a Buildroot Linux kernel.
There is nothing available on the Internet that provides the whole picture. Also, with the changes in Bluez from version 4 to 5, things that did
work, stopped working. In my application, the Pi Zero is the Bluetooth server, connecting to any client mobile device in serial comms mode (SP).  The Pi is headless -
it does not have a user display, keyboard, or any user interface.  Therefore, pairing must not require a PIN or acceptance dialog from the Pi.
In my application, the Pi drives an 18bit parallel video display device, the config.txt file has commands in it to set it up. I have also implemented
the USB gadget so that I can SSH into the Pi with a USB tether to a PC running a terminal (PuTTY) program.
All Bluetooth control (discovery, listening, connection) must be done programmatically - in a C application running on the Pi.  I am not claiming
that this is the only way, or best way to accomplish this - I'm sure there are better ways.  At least this works for me.

Linux Version: 5.10.1
Buildroot Version: 2020-11-1314
Bluez Version: 5.55

Folder and file description/usage:

-Buildroot
--board

---pi0wbluetooth - COPY THIS ENTIRE FOLDER INTO THE BUILDROOT "board" FOLDER.

----bootfiles

-----cmdline.txt - this file will be placed in the BOOT partition. It disables the console and splash to the display.

-----config.txt - this file will be placed in the BOOT partition. It sets up the USB gadget, i2c-gpio, and parallel video.

You should alter these two files as needed for your application. The USB gadget (SSH) is very useful in a headless application.

----linux_config

-----pi0wbluetooth_linux_config - this file is used by Buildroot to configure the Linux kernel and automatically load
the needed drivers.  Some drivers needed by my app are loaded but may not be needed.  Strongly suggested that you use
this configuration to start, then modify once Bluetooth is working for you.

----overlay

-----boot - this is an empty directory. I use it to mount the BOOT partition during development work.
-----etc - this directory is overlaid on the one created by Buildroot. It contains the initialization files needed
for Bluetooth and the USB Gadget (SSH).

------bluetooth

-------main.conf - this file is used by bluetoothd.  The settings here are important. They will ensure that pairing works
without a PIN or yes/no requirement on the headless Pi.  It also enables the Bluetooth adapter on bootup.
------dbus-1 - this directory and files within are the same as the one Buildroot creates.
------init.d - this directory contains the scripts run at bootup. I only detail important ones below:
-------S20urandom - random number generator (along with have ged) used by Bluetooth and SSH. The first boot will take
more time as these are "seeded".
-------S21haveged - see above.
-------S40bluetooth - this script starts the bluetooth daemon (bluetoothd). The big change to this file is the added -C option.
-------S40network - this script brings up the network on USB0 for the USB gadget (SSH).
-------S50sshd - this script starts the SSH daemon and generates keys on first boot (takes time first boot).
-------S60btattach - this script does the attachment of the bluetooth adapter to serial port ttyAMA0. IMPORTANT.
-------S70apprun - this script uses sdptool to enable serial communication over bluetooth - IMPORTANT.
I also do some I2C settings for my app here, and then I start my app. 
------network
-------interfaces - this is where the USB Gadget is configured. NOTE: I use IP address 192.168.69.1 which will most likely
require that you reset your PC's IP address used by the gadget.  You could also change this IP address...
------ssh - this directory and files within are the same as the one Buildroot creates.
------ssl - this directory and files within are the same as the one Buildroot creates.
------inittab - this file is modified to remove getty not used for headless
------profile - this file is modified to provide a nicer command line and sets nano as the default editor
------[other files] - these are as provided by Buildroot - do not alter.
----genimage-pi0wbluetooth.cfg - this file is used by Buildroot "make" to write the files for the OS sd card. DO NOT ALTER.
----post-build.sh - this file is used by Buildroot. DO NOT ALTER.
----post-image.sh - this file is used by Buildroot - DO NOT ALTER.
----readme.txt - this file provides instructions for making the SD CARD - you should READ IT!
--configs - 
---pi0wbluetooth_defconfig - COPY THIS ENTIRE FOLDER INTO THE BUILDROOT "configs" FOLDER.
This is the file you use to configure Buildroot. "make pi0wbluetooth_defconfig" will configure, 
then run "make" to build your OS.  This takes a LONG time.
