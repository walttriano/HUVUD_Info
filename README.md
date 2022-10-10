# HUVUD Notes:

## Purchasing:
1. https://lukeslabonline.com/products/huvud

## Installation:
1.  https://www.notion.so/Instructions-Getting-a-Huvud-Toolhead-Working-with-Klipper-611ebb87efba4e22ae3b0641e5267ab3#35159f58eeff4fb6b1e08e27784550a6
2. YouTube Guide by Eddie the Engineer: https://www.youtube.com/watch?v=XkcxSQhRR3I

## References
1. https://www.klipper3d.org/CANBUS.html?h=canbus
2. https://www.waveshare.com/wiki/RS485_CAN_HAT
3. https://github.com/bondus/KlipperToolboard

## Steps
### Prepare the RPi / Waveshare Board:

1. sudo nano /etc/network/interfaces.d/can0  (Creates the file)

	Paste the following:
	auto can0
	iface can0 can static
		bitrate 500000                            <<<<< This value must match the value in make menuconfig
		up ifconfig $IFACE txqueuelen 128
	
	Note: bitrade must match klipper value in make menuconfig

2. 	sudo nano /boot/config.txt

	paste the following below [all]:
	dtoverlay=mcp2515-can0,oscillator=12000000,interrupt=25,spimaxfrequency=2000000

3.  Reboot the RPi
	sudo reboot
	
4.  Check the CAN interface
	ip addr list
	
	This should return something like the following.  Look for "UP"
	can0: <NOARP,UP,LOWER_UP,ECHO> mtu 16 qdisc pfifo_fast state UP group default qlen 128
    link/can

5.	Update klipper to the latest version

### Flash the Huvud:

1.	cd klipper
2.  make menuconfig
	
	[*] Enable extra low-level configuration options
		Micro-controller Architecture (STMicroelectronics STM32)  --->
		Processor model (STM32F103)  --->
	[ ] Only 10KiB of RAM (for rare stm32f103x6 variant)
	[ ] Disable SWD at startup (for GigaDevice stm32f103 clones)
		Bootloader offset (2KiB bootloader (HID Bootloader))  --->
		Clock Reference (8 MHz crystal)  --->
		Communication interface (CAN bus (on PB8/PB9))  --->
	(500000) CAN bus speed
	()  GPIO pins to set at micro-controller startup

3.	make clean
4.	make

5.	Connect boot pin
6.	Attach USB cable to Huvud from RPi
7.	Power Huvud (Huvud light should be flashing)

8.	make flash FLASH_DEVICE=1209:beba

9.  Shutdown everything
5.	Remove pin
6.	Detach USB cable to Huvud from RPi
7.	Power Huvud (Huvud light should be steady)
8.  Connect "H" and "L" wires to Huvud - Waveshare
9.	Power on everything
10.	~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0

