---
layout: standard
title: Pen Input on Linux
---
### Pen Input on Linux

I use ElementaryOS, so these instructions will be most relevant for Ubuntu and Debian like distributions.

For calibrating the pen, you can use `xinput_calibrator`.  It'll display four points to calibrate based on, which isn't ideal - I'm still looking for a nine point calibrator.  To install it, run
```bash
sudo apt install xinput-calibrator
```
For now I'm fine running the calibrator whenever I need to use the pen input, but once I figure out a better calibration system I'll likely detail here how to set the config file.

Notes for future me: Maybe try the `--misclick 0` argument for fine control - maybe that'll fix the issue I had today.

Other issues at the moment:
* The ElementaryOS settings app closes when I try and set up screen mirroring between my laptop and the drawing tablet.
* The default pen config maps to the wrong display, so I have to cycle `SUPER+P` until the drawing tablet is the only display.
