---
layout: standard
title: Tips for Macromedia Flash MX 2004
---
## Tips for Macromedia Flash MX 2004

The program works nearly flawlessly under WINE (no pressure sensitive pen input); On Windows 8.1, use Windows XP compatibility setting.  This *is* a twenty year old program at this point, so save often.

### Exporting SVG

#### GUI Route
While checking my notes and researching to put this together, I found <https://stackoverflow.com/a/21256104>, which links to Very New Media's Flash2SVG tool ([Direct Download Link](https://www.verynewmedia.com/download/699/)).  This appears to be a gui app that uses the same `as3swf` library as the CLI program I've used extensively.

It's an AIR app, so we need to obtain an Adobe AIR runtime.  HARMAN took over support for Adobe AIR , so you can obtain an up-to-date runtime at <https://airsdk.harman.com/runtime>.  Do note that they discontinued linux support, so we're kind of limited to doing this on Macs or Windows machines.

#### CLI Route
Previously, I used <https://github.com/flying-sheep/SWF2SVG>.  It is also an AIR app.  The main issue with this tool over the GUI route is that exporting omits the viewBox.  You will need to manually set the viewBox in a text editor.

### Importing SVG
[Inkscape](https://inkscape.org/) provides two "Save As..." options of interest to us: WMF (Windows Metafile) and EMF (Enhanced Metafile).  There are some subtle differences in support between the two, but I typically use WMF.  Regardless of which one is used, not all SVG features are supported or converted over.

The main issue with WMF and EMF is scaling; they're imported as a bit smaller than they should be.

#### Dead Ends That I'll Keep Looking In To

* Inkscape can save to FXG (Flash XML Graphics).  However, FXG is from the Adobe era.  We'll need to find a way to convert FXG to SWF or FLA. (`mxmlc` from the AIR SDK?)
  * This FXG to MXML app doesn't work under Ruffle: <https://github.com/JonDum/FXG-to-MXML-Converter/tree/master>.  Is it even needed, or can `mxmlc` operate on FXG's?  Either way, the code is quite simple and can be reimplemented in JS with minimal effort.
