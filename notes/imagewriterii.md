---
layout: standard
title: Using an Apple Image Writer II printer
date: 2026-03-02
---
## Using an Apple ImageWriter II printer
### General Tips
When using letter sheets of paper, do not use the Form Feed button - that will clear the top margin and start printing immediately at the top of the page.  My current workaround for one-page documents is to Form Feed, shut the printer off, turn it back on, and then click Print from the computer.

### Windows 10
[whizkid2001's reddit thread](https://old.reddit.com/r/VintageApple/comments/zbugme/my_final_college_paper_printed_off_an_imagewriter/iyu1kr3/) provides the essential instructions required for getting the Windows XP driver for the C-Itoh 8510 working with the ImageWriter II on modern versions of Windows.  (I attempted to follow these instructions on one of my Windows 8.1 machines and failed, but it worked on my Windows 10 machine.)  If you prefer a video format, [Sam Davisson's video](https://www.youtube.com/watch?v=uH5UZ3cYRys) presents the same core process.

#### Further Experimentation
Editing the GPD file to set the DPI to 160x144 (which is what Wikipedia says the DPI for this printer is) didn't seem to have a noticable quality difference.  One strange quirk is that [the Ghostscript documentation for the ImageWriter II](https://ghostscript.readthedocs.io/en/latest/Devices.html#apple-s-dot-matrix-printer-and-imagewriters) claims that the apple technical manual recommends setting the printer to Elite character pitch - while the above Windows instructions edit the 8510 driver from Elite (`E`) to Pica Proportional (`p`).  Given that Elite Proportional is `P`, I wonder if that's a transcription error - or if it even matters.  I'll have to test that when I have the time.
