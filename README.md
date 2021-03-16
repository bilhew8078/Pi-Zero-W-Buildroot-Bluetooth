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

Bluez Version: 5.55

Folder and file description/usage:

