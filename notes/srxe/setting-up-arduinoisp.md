---
layout: standard
title: Use an Arduino Uno as a programmer for the Smart Response XE
---
## Use an Arduino Uno as a programmer for the Smart Response XE

### ArduinoISP

We don't need a fancy ISP programmer, we can simply use an Arduino UNO [[Source 3](https://hackaday.io/project/192010-smoke-testing-a-smart-response-xe)] for this.

From the Arduino IDE 1.8.19, navigate within the menu bar: File &rarr; Examples &rarr; 11. ArduinoISP &rarr; ArduinoISP.

Compile and upload to the Arduino UNO.

#### Wiring Guide

|Arduino Pin|SRXE Pin|
|--|--|
|13|SCK (clock)|
|12|MISO|
|11|MOSI|
|10|RST|
|GND|GND|
|3.3V|VCC|

I have the Enigma Development Adapter from [Bradán Lane](https://www.tindie.com/products/bradanlane/smart-response-xe-developer-kit/), which makes mapping the pogo pins easy.

Only connect 3.3V to VCC if the batteries are removed from the SRXE [[Source 1](https://docs.google.com/document/d/1TT3Tmx0-dsvzmA0lSYiwq4LR293aMV1dJWhriqxD-Po/preview), p. 6].

(See the Figures at the bottom of the page for photos of an Arduino UNO and the Enigma board wired together.)

### Borrow the IDE's avrdude to write a hex file to the SRXE

We can borrow `avrdude` from the Arduino IDE [[Source 2](https://forum.arduino.cc/t/the-simplest-way-to-upload-a-compiled-hex-file-without-the-ide/401996/2)]:

> 1. Select File > Preferences from the Arduino IDE menus.
> 2. Check the box next to "Show verbose output during: [] upload".
> 3. Click the OK button.
> 4. Select Sketch > Upload from the Arduino IDE menus.
> 5. Wait for the upload to finish.
> 6. Scroll up in the black output panel at the bottom of the Arduino IDE window until you see the avrdude command.

We only need the first two parts of the avrdude command from the IDE: the one that ends with `avrdude` and the one that ends with `avrdude.conf`.

(For my system, this was quite verbose.)
```
/private/var/folders/yh/t6g9xn1909sbc3vw9s7kkxq40000gn/T/AppTranslocation/C90B91C7-D03D-4351-998B-56923231A2F0/d/Arduino.app/Contents/Java/hardware/tools/avr/bin/avrdude -C/private/var/folders/yh/t6g9xn1909sbc3vw9s7kkxq40000gn/T/AppTranslocation/C90B91C7-D03D-4351-998B-56923231A2F0/d/Arduino.app/Contents/Java/hardware/tools/avr/etc/avrdude.conf
```

Now, we can paste on the other half of our command, courtesy of [[Source 1](https://docs.google.com/document/d/1TT3Tmx0-dsvzmA0lSYiwq4LR293aMV1dJWhriqxD-Po/preview), p. 8]:
```
-pm128rfa1 -cstk500v1 -PCOM4 -b19200 -e -Ulfuse:w:0xEE:m -Uhfuse:w:0x93:m -Uefuse:w:0xFC:m -Uflash:w:FILENAME.hex:i
```

Things we'll have to change in that second half: `COM4` and `FILENAME.hex`.  (I did wonder if I needed to replace `-cstk500v1`, but it worked as is.)  COM4 will be the port your Arduino UNO is connected to, which you can check from the IDE menu bar: Tools &rarr; Port.

For me, the port was `/dev/cu.usbmodem1440`.  You can set the path to your desired hex file in place of `FILENAME.hex`.

All together, my resultant command looked like:
```
/private/var/folders/yh/t6g9xn1909sbc3vw9s7kkxq40000gn/T/AppTranslocation/C90B91C7-D03D-4351-998B-56923231A2F0/d/Arduino.app/Contents/Java/hardware/tools/avr/bin/avrdude -C/private/var/folders/yh/t6g9xn1909sbc3vw9s7kkxq40000gn/T/AppTranslocation/C90B91C7-D03D-4351-998B-56923231A2F0/d/Arduino.app/Contents/Java/hardware/tools/avr/etc/avrdude.conf -pm128rfa1 -cstk500v1 -P/dev/cu.usbmodem14401 -b19200 -e -Ulfuse:w:0xEE:m -Uhfuse:w:0x93:m -Uefuse:w:0xFC:m -Uflash:w:~/Downloads/smartresponse-xe-interlaced-paintscreen/VLQP_AB.ino.rf128.hex:i
```

### Sources
1. <https://docs.google.com/document/d/1TT3Tmx0-dsvzmA0lSYiwq4LR293aMV1dJWhriqxD-Po/preview>
2. <https://forum.arduino.cc/t/the-simplest-way-to-upload-a-compiled-hex-file-without-the-ide/401996/2>
3. <https://hackaday.io/project/192010-smoke-testing-a-smart-response-xe>

### Figures

![Arduino UNO with colored wires: orange to ground, yellow to 13, green to 12, blue to 11, purple to 10, grey to 3.3V.](https://github.com/user-attachments/assets/9b6f58f6-f6c6-4b2f-bbba-75bb0b7ce0ae)

![Enigma Development Adapter with colored wires: orange to ground, yellow to SCK, green to MISO, blue to MOSI, purple to RST.](https://github.com/user-attachments/assets/f77b5d22-9737-4c22-a097-36271258b528)
