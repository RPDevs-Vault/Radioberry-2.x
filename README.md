RadioBerry V2.0 - Ham radio cape for Raspberry PI
==============================================

## Main purpose of the project:

- Building a HAM Radio
- Learning (from noob to guru)

WIKI FOR MORE DETAILS:  https://github.com/pa3gsb/Radioberry-2.x/wiki

## Radioberry:

- Raspberry PI
- Radio extension board (cape)
	- using AD9866 (12 bit)  for RX and TX modes.

	![Radioberry-2.x](docs/gallery/front.JPG)
	![Radioberry-2.x](docs/gallery/radioberry-in-case-small.jpg)
	![Radioberry-2.x](docs/gallery/back.JPG)
	![Radioberry-2.x](docs/gallery/rb+rpi-front.JPG)
	![Radioberry-2.x](docs/gallery/rb+rpi-side.JPG)
	![Radioberry-2.x](docs/gallery/radioberry-open-incase-small.jpg)
	

## Powered by Raspberry Pi

Radioberry is designed to run on Raspberry Pi hardware and is licensed to use the official “Powered by Raspberry Pi” logo.

Raspberry Pi is a trademark of Raspberry Pi Ltd.

<p align="center">
  <img src="docs/assets/powered-by-raspberry-pi.png" alt="Powered by Raspberry Pi" width="220">
</p>



## Building

### Install dependencies
	sudo apt-get update
	sudo apt-get install git libpulse-dev libgtk-3-dev libasound2-dev libcurl4-openssl-dev \
		libi2c-dev libgpiod-dev libusb-1.0-0-dev device-tree-compiler libfftw3-dev

### Obtain the source codes
	cd /tmp
	git clone --depth=1 https://github.com/pa3gsb/Radioberry-2.x.git
	cd Radioberry-2.x
	git submodule update --init --recursive --depth=1

### Build the software
	sudo cp /tmp/Radioberry-2.x/wdsp/libwdsp.so /usr/local/lib/
	sudo ldconfig
	make CFLAGS="-g -Wno-deprecated-declarations -I../wdsp" LDFLAGS="-L../wdsp" -j$(nproc)

## Installation
	sudo make install FPGATYPE=CL016
