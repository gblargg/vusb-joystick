V-USB Joystick Example
======================
This uses V-USB to implement a USB joystick with minimal code. It supports up, down, left, right and two buttons (and can trivially handle 6 more).

The code runs on Thomas Fischl's 12MHz atmega8 USBasp programmer hardware, which supports V-USB, has several I/O pins, and is available cheaply. It can be programmed using a second USBasp programmer or by loading something like USBaspLoader on it to allow self-reprogramming.

I wrote this example because none of the other joystick projects I found implemented just a simple joystick with a minimal amount of code. I was able to pare this example down to very little code so that the essence is easier to understand. Thanks to others whose joystick allowed me to write this.


Files
-----
	main.c          USB joystick
	usbconfig.h     Configures V-USB
	usbdrv/         Software-based USB driver; see http://www.obdev.at/vusb/
	Makefile        Compiles code and flashes it

	README          This documentation
	License.txt     GPL v2 + V-USB additions
	schematic.png   Schematic of USBasp hardware
	usbasp.jpg      USBasp hardware this was tested on


Building
--------
* Use second USBasp programmer to reprogram first. Connect them together via ISP ribbon cable. Close JP2 on first one to allow reprogramming its firmware. Connect second one to USB.

* Build and flash firmware:

	make flash

* Disconnect second programmer from USB and ISP ribbon.

* Plug first one into USB and verify that it shows up as joystick.

* Run a joystick test program that shows what's being pressed. On Linux:

	jstest --event /dev/input/js0

* Connect GND to one of the control pins and verify that button presses show up (be sure to avoid connecting it to +5V!). LED should toggle between on and off each time you close/open a button pin.


Hardware
--------
Controls (connect to GND to press):

		       ------
	 Down PB3 | 1  2 | +5V
		  N/C | 3  4 | PD1 Button 1
	   Up PB2   5  6 | PD0 Button 0
	Right PB5 | 7  8 | GND
	 Left PB4 | 9 10 | GND
		      ------
	Male connector on USBasp programmer


Operation
---------
V-USB implements a low-speed USB device in software. When connected, it tells the host that it's a USB joystick. It also tells the host what controls it has and the format of the report it sends (usbHidReportDescriptor[] in main.c). Then when the joystick changes it sends the new state of the controls to the host. This can only be done every 8ms (1/125 second), when the host asks the device if it has any updates. There are ways to make the host check more often (up to 1000 times a second) by changing its hid polling rate.

-- 
Shay Green <gblargg@gmail.com>
