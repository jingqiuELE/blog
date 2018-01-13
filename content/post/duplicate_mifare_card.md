+++
date = "2018-01-13T23:12:59+08:00"  
draft = false  
title = "duplicate mifare classic 1k card"  

+++

### Hardware
 * NFC module:  
   PN532 NFC RFID module kits (elechouse):  
   http://www.elechouse.com/elechouse/index.php?main_page=product_info&cPath=90_93&products_id=2205  
   Chip: NXP PN532  
   Module just breaks out all the IO pins of the chip.  
   Mifare cards supported  
 * CP2102 single chip USB to UART bridge break out module  
   Linux comes with driver for cp2102. Plug and play  
 * Blank card  

### Software
 * Need userspace app to decode data received from USB <-- UART  
   [libnfc](https://github.com/nfc-tools/libnfc)
 * Tools to crack mifare card:  
   [mfoc](https://github.com/nfc-tools/mfoc)

### Commands
   See Reference:  
     * https://firefart.at/post/how-to-crack-mifare-classic-cards/
     * https://github.com/cirias/blog/blob/master/content/mifare-cracking.md

### Experiment
   The access system here is a mifare classic 1k card. It took less than 5 minutes to crack it and make a copy.
