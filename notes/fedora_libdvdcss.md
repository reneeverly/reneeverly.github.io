---
title: Getting encrypted DVDs to work in VLC on Fedora Linux
layout: standard
---
## Getting encrypted DVDs to work in VLC on Fedora Linux
The main issue at play here is the absence of libdvdcss.  For ideological reasons, it isn't included in the default repositories, but rpmfusion has it.
```bash
sudo dnf install vlc
sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install rpmfusion-free-release-tainted
sudo dnf install libdvdcss
```
