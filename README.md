# Raspberry Pi Transmission Daemon Setup

Shows how to setup Transmission Client on headless Raspberry Pi installation and external usb hard drive or usb flash drive.

## Getting Started

These instructions will help you install and configure **Transmission Daemon Client** and format usb flash drive or usb external hard drive as linux partition and auto mount on boot. You will be able to access the torrent client via the daemons web interface.

### Prerequisites

* Raspberry Pi 3
* Rasbian Lite Stretch or later 
* 8gb Micro SD Card
* Usb flash drive(min 16gb+) or usb hard drive(min 50gb+)

### Installing

Step-by-step instruction on how to install Transmission Daemon and to config it and add external storage for storing the torrents. I will assume that you have lastest version of Rasbian Lite installed, If not you can download it from [Raspberry Pi Foundation](https://www.raspberrypi.org).

1. We have make sure Rasbian ropositories update and running the lastest versions.
We are going to run two commands to update the Rasbian.

```
sudo apt-get update && sudo apt-get dist-upgrade -y
```