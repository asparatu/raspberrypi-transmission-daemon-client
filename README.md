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

Step-by-step instruction on how to install Transmission Daemon and to config it and add external storage for storing the torrents. I will assume that you have latest version of Rasbian Lite installed, If not you can download it from [Raspberry Pi Foundation](https://www.raspberrypi.org).

#### 1. Make sure Rasbian repositories update and running the latest versions.
We are going to run two commands to update the Rasbian.

```bash
sudo apt-get update && sudo apt-get dist-upgrade -y
```

#### 2. Install Transmission Daemon

This simplest part fo the whole installation. \
Type this command into the terminal window.

```bash
sudo apt-get install transmission-daemon -y
```

#### 3. Create the Linux partition

The storage device we will use will be permanently attached to the Raspberry Pi, so we will make it an ext4 partition.

First we need to find the partition mount point.
```bash
sudo fdisk -l
```

We are looking for disk **/dev/sda**, you should get output like this:

```
Disk /dev/sda: 960 MiB, 1006632960 bytes, 1966080 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xaebac6bb

Device     Boot Start     End Sectors  Size Id Type
/dev/sda1        2048 1966079 1964032  959M 83 Linux
```

**Caution**: *Make sure you double check make sure use the correct disk or you can destroy the Rasbian installation!*

We are going delete the current partition on this storage device and create new linux partition and format as ext4 file system.

**Note**: This will remove all the current files on the storage device.

```bash
sudo fdisk /dev/sda
```

When you run fdisk this will display:
```
Welcome to fdisk (util-linux 2.29.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help):
```
You need to delete the existing partitions on the drive. To this you **press d** and **enter**.

**Note**: If you have more then one partition, it will ask you which one to delete. You can use the default which is 1 and just repeat to all them have been deleted.

```bash
Command (m for help): d
Selected partition 1
Partition 1 has been deleted.
```

Next you will have to create new partition. To do this press **n** and **enter**

```bash
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-1966079, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-1966079, default 1966079):

Created a new partition 1 of type 'Linux' and of size 959 MiB.
```

Last thing we have do write the changes to the disk. To this press **w** and **enter**
```
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

#### 4. Format the disk

We are going to format the disk. Depending size it might take few minutes.

```bash
sudo mkfs.ext4 /dev/sda1
```

When done should display something like this:

```bash
mke2fs 1.43.4 (31-Jan-2017)
Creating filesystem with 245504 4k blocks and 61440 inodes
Filesystem UUID: 8cf28d0a-819b-4d65-ae5b-63c8c9400c7f
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done
```

#### 5. Mount the drive to directory

We need to create mount point in the media directory and change the owner to pi user and group

```bash
cd /media
sudo mkdir storage
sudo mount /dev/sda1 /media/storage
sudo chown pi:pi /media/storage
```

#### 6. Create the torrent directories

We are going to create two directories to store the torrents in, one will be for **completed** and one will be fore **incomplete** torrents.

```bash
mkdir torrent
cd torrent
mkdir complete
mkdir inprogress
```

We need to change the owner to the debian-transmission user and group by doing this there will be no read or write issue, when downloading the torrent files.

```bash
sudo chown -R debian-transmission:debian-transmission /media/storage/torrent
```

## Authors

**Shane Saunders** - *Initial work* - [asparatu](https://github.com/asparatu)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.