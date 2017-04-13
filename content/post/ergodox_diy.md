+++
date = "2017-04-09T18:34:06+08:00"
title = "DIY ergodox keyboard"
+++

## How does a modern computer keyboard work?
See here:  
http://www.dribin.org/dave/keyboard/one\_html/  
The data from a keyboard comes in the form of scancodes, which are produced by key presses and can be recognized by the computer. It seems that the USB protocol used for keyboard has a limitation of 6 scan codes for a single transfer, which in most cases is enough.
By mapping scancode sent to a computer to a different scancode set, it is possible to change the keyboard layout seen by the computer without changing the real physical keyboard.
Thus, it is possible to switch to Dvorak layout on a computer by changing the scan code map scheme.  
References to scancodes can be found here:  
http://www.win.tue.nl/~aeb/linux/kbd/scancodes-1.html

## What is ergodox keyboad?
It is an opensource design. Details:  
https://www.ergodox.io/

## Problems met && solved during the DIY process
1. Purchase the parts, some are hard to find at the time. For example:  
   As the 3.5mm TRRS plug cable is hard to get, 4 DuPont lines were used to replace it.(The cable was supposed to connect the two keyboads).
2. Learn to do soldering and use other diy tools.
3. Identified a Fuse bit setting issue which caused some pins not functioning as GPIO.
4. Always verify before doing anything hard to reverse.


