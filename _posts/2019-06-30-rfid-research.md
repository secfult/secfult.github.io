---
layout: post
title: RFID Research
categories: [physical, wireless, rfid]
tags: [frequency, passive, active, MFRC522, MIFARE]
fullview: true
comments: true
---

#### Australian Laws

Make yourself aware of Australian laws around RFID spectrums/power limits. Reading the [ACMA RFID devices information page](https://www.acma.gov.au/Industry/Suppliers/Product-supply-and-compliance/Commonly-supplied-equipment/rfid-devices) is a good start.


#### What is RFID?

Passive RFID tags have a coil and a chip with data on it. An RFID reader has a coil in it that creates an electronic field. Passive tags collect energy from a nearby RFID reader's interrogating radio waves. This all works on the principle of inductive coupling. That's all I'll summarise because I'm not an electrical engineer :)


#### Types of RFID cards

##### Low frequency, or LF (125 – 134 kHz)

* Read range: cm's
* Possibly includes pet microchips

##### High frequency, or HF (13.56 MHz)
* Read range: max 1 meter
* NFC and HID cards

	Readers/Writers
	* MFRC522 (data sheet: https://www.nxp.com/docs/en/data-sheet/MFRC522.pdf)
		* Supports ISO/IEC 14443 A/MIFARE and NTAG, MF1xxS20, MF1xxS70 and MF1xxS50 products
		 Following host interfaces are provided:
			* Serial Peripheral Interface (SPI)
			* Serial UART (similar to RS232 with voltage levels dependant on pin voltage supply)
			* I2C-bus interface
		* Typical operating distance in Read/Write mode up to 50 mm depending on the antenna size and tuning
		* Supports MF1xxS20, MF1xxS70 and MF1xxS50 encryption in Read/Write mode
		* 2.5 V to 3.3 V power supply

##### Ultra-high frequency, or UHF (433, and 860-960 MHz)
* Cannot pass through metal or water
* cm's to 15m +
* Used for tolls, parking access
* RFID devices in Australia can only be operated between 915 and 928 MHz


I purchased the following to get started which totalled ~$40 AUD, so I'll update this gist when I get to play around with the stuff.
* Arduino (clone) starter kit including MFRC522 (for HF RFID): https://www.ebay.com.au/itm/262948127587
* 125KHz reader/writer: https://www.aliexpress.com/item/32878540959.html
