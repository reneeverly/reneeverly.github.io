---
layout: standard
title: Pen Input on Linux
---
### Pen Input on Linux

I use ElementaryOS, so these instructions will be most relevant for Ubuntu and Debian like distributions.  For further reference, the pen/display tablet I am calibrating is an Ugee 1910b.

For calibrating the pen, you can use `xinput_calibrator`.  It'll display four points to calibrate based on, which isn't ideal - I'm still looking for a nine point calibrator.  To install it, run
```bash
sudo apt install xinput-calibrator
```
For some systems, xinput_calibrator won't work out of the box.  Following [this AskUbuntu answer](https://askubuntu.com/questions/1188624/trouble-with-xinput-calibrator-on-a-toughbook-cf-19), I swapped libinput for evdev.
```bash
sudo apt install xserver-xorg-input-evdev
sudo apt remove xserver-xorg-input-libinput
```
However, a quick restart showed that my trackpad no longer worked.  Recommendations from [the Linux Mint Forums](https://forums.linuxmint.com/viewtopic.php?t=378157) pointed to installing another xorg input package, which brought the trackpad back.
```bash
sudo apt install xserver-xorg-input-synaptics
```
With all of this wrangled, `xinput_calibrator --list` now shows my display tablet, and `xinput_calibrator --device 10` applies the calibration immediately after running.

{::comment}
Notes for future me: Maybe try the `--misclick 0` argument for fine control - maybe that'll fix the issue I had today.
{:/comment}

Other issues at the moment:
* ~~The ElementaryOS settings app closes when I try and set up screen mirroring between my laptop and the drawing tablet.~~
* ~~The default pen config maps to the wrong display, so I have to cycle `SUPER+P` until the drawing tablet is the only display.~~
* ElementaryOS (as of 19 May 2025) now defaults to mirroring at 1280x800.  Correct aspect ratio for the tablet, but not at max resolution.  I'll take it.
