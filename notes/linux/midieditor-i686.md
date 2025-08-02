---
layout: default
title: Installing MidiEditor on Debian i686
---
## Installing MidiEditor on Debian i686
### Qt5 Dependencies
```bash
sudo apt install qt5-qmake
sudo apt install qtbase5-dev
sudo apt install qtmultimedia5-dev libqt5multimediawidgets5 libqt5multimedia5-plugins libqt5multimedia5
```
### MidiEditor dependencies and build process
Clone the [git repo](https://github.com/markusschwenk/midieditor).
```bash
sudo apt-get install libasound2-dev
chmod +x building/build-linux.sh
./building/build-linux.sh
```
It took about 20 minutes for *firebird*, my Pentium 4 system, to build MidiEditor.
