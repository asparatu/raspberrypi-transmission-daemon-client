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

#### 1. Make sure Rasbian repositories update and running the latest versions

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

```bash
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

```bash
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

```bash
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

#### 5. Create storage directory and mount the drive to directory

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
mkdir torrent/complete
mkdir torrent/inprogress
```

#### 7. Change owners and groups on torrent directory

We need to change the owner to the debian-transmission user and group by doing this there will be no read or write issue, when downloading the torrent files.

```bash
sudo chown -R debian-transmission:debian-transmission /media/storage/torrent
sudo find /media/storage/torrent/ -type d -exec chmod 755 {} \;
sudo find /media/storage/torrent/ -type f -exec chmod 644 {} \;
```

#### 8. Edit the fstab file to auto mount the storage

We are going to edit the **fstab** file in **etc** directory, so the storage will not need to be manually mounted after the Raspberry Pi has booted. If the storage drive is not found, the boot process will not fail, but timeout and finish the booting of the Raspberry Pi.

We need to make backup copy of the fstab, first, incase there is any problem we can just copy over the modified file.

```bash
sudo cp /etc/fstab /etc/fstab.bak
```

We need to get the **UUID** of the storage device, the reason we use the **UUID** is, if you add another Usb Storage Device, if could use the same device name as current one and mount of drive can fail.

```bash
sudo sudo ls -l /dev/disk/by-uuid/
```

That should display something like this:

```bash
lrwxrwxrwx 1 root root 15 Jul  8 12:59 16D2-035F -> ../../mmcblk0p1
lrwxrwxrwx 1 root root 10 Jul  8 12:59 8cf28d0a-819b-4d65-ae5b-63c8c9400c7f -> ../../sda1
lrwxrwxrwx 1 root root 15 Jul  8 12:59 d065e631-6b9d-48c0-a8fe-e663b42828e0 -> ../../mmcblk0p2
```

What we need is the symbolic link to  /sda1, so you need to copy **UUID**.

```bash
8cf28d0a-819b-4d65-ae5b-63c8c9400c7f (UUID)
```

We are going to edit the **fstab** file in the **etc** directory.

```bash
sudo nano /etc/fstab
```

We are going to add the mount point reference to the fstab file

```bash
UUID=xxxxx-xxxxxx    /media/storage    ext4   nofail,defaults    0   0
```

Where the UUID=xxxxx-xxxxxx is you need to copy UUID for the **sda1** drive. When you are done it should look like this:

```bash
UUID=8cf28d0a-819b-4d65-ae5b-63c8c9400c7f    /media/storage    ext4   nofail,defaults    0   0
```

**Note**: *Do not use my **UUID** because will not work on your system.*

Just above comments in the fstab file **press enter** and copy the the line you want to enter, move the cursors to the new line and  **right click** and what copied it will be pasted in the new line.

Should like something like this:

```bash
PARTUUID=06b4c3fb-01  /boot           vfat    defaults          0       2
PARTUUID=06b4c3fb-02  /               ext4    defaults,noatime  0       1
UUID=8cf28d0a-819b-4d65-ae5b-63c8c9400c7f    /media/storage    ext4   nofail,defaults    0   0
# a swapfile is not a swap partition, no line here
#   use  dphys-swapfile swap[on|off]  for that
```

We are done editing the **fstab** file and we have to save the file now.

```bash
Press **ctrl + x** and press *Y* and *enter*
```

Reboot the Raspberry Pi now by using this command

```bash
sudo reboot
```

You have wait maybe 15 seconds for to reboot and mount the storage device. If everything worked correctly you can log back into the Raspberry Pi.

To check to see if mount the storage or fail, we are going typ this command:

```bash
sudo df
```

This will give all the mount points and what space is left on the drives.
you should see the **/dev/sda1** mounted to **/media/storage**

#### 9. How to change the default hostname

We are going to change the default hostname of the Raspberry Pi because all new installation have hostname **raspberrypi**.

We are going to edit the **hosts** and **hostname** file in the **etc** directory.

```bash
sudo nano /etc/hosts
```

Look for the **127.0.0.1 raspberrypi** change the **raspberrypi** to what you want your hostname to be.

Save the file by pressing **ctrl + x** and Press **y** and press **Enter**

We need to change the name in hostname file

```bash
sudo nano /etc/hostname
```

Change the **raspberrypi** to what you changed in **hosts** file and save the file.

Reboot the Raspberry Pi so the new hostname can be used. You should be able to connect to the Raspberry Pi using **(yourhostname).local** instead of **raspberrypi.local**.

#### 10. Edit the Transmission Daemon configuration file

There is only few things we are going to edit on the configuration file. You can have setup so you need to logon or not and set the hostname and subnet to allows access to the web UI.

What we need we will edit is:

* blocklist-url
* blocklist-enabled
* download-dir
* download-queue-size
* incomplete-dir
* incomplete-dir-enabled
* rpc-host-whitelist
* rpc-host-whitelist-enabled
* rpc-whitelist-enabled
* max-peers-global
* peer-limit-per-torrent
* peer-port-random-on-start

The first thing we need to do is stop the transmission daemon service, if we do not do this any changes made will be over written by default file.

You need to enter this command:

```bash
sudo service transmission-daemon stop
```

We can now edit the settings.json file.

```bash
sudo nano /etc/transmission-daemon/settings.json
```

The first thing we will edit and enable the blocklist. This has list of malicious added not to download from. In the web UI, you have to download the list. To do that, click **Edit Preferences**, click **Peers**, then click **Update**

```bash
"blocklist-enabled": true,
"blocklist-url": "http://list.iblocklist.com/?list=ydxerpxkpcfqjaybcssw&fileformat=p2p&archiveformat=gz",
```

We are going to set the download preferences.

Set the **download-dir** to the **complete** directory.

```bash
"download-dir": "/media/storage/torrent/complete",
```

Set the **download-queue-size to **1** or **2** this amount of active torrents being download at once. Depending on your connection speed, they will download fast even at 1 or 2.

```bash
"download-queue-size": 1,
```

There is **incomplete directory** this used to store all the torrents queue and actively being download. When this are marked complete they are copied the complete directory.

We will enable and set the directory.

```bash
"incomplete-dir": "/media/data/torrent/inprogress",
"incomplete-dir-enabled": true,
```

Set the **peer-limit-per-torrent** to **100** this max number of torrent peers that torrent can have.

```bash
"peer-limit-per-torrent": 100,
```

Set the **max-peers-global** to **200** this max number of global peers you can have.

```bash
"max-peers-global": 200,
```

Set the **peer-port-random-on-start** to **true** this will make the torrent port to change every time you restart the server or service. This good if your ISP will block current ports.

```bash
"peer-port-random-on-start": true,
```

You should also change **encryption** to **1**, so all the connection will be encrypted.

```bash
"encryption": 1,
```

We going to disable the web ui user security because this will be on local network, but we will set the host white list has enable that.

Set the **rpc-whitelist-enabled** to **false**, which will disable the login function.

```bash
"rpc-whitelist-enabled": false,
```

Set the **rpc-host-whitelist-enabled** to **true**, which will let any host to connect to the web ui.

```bash
"rpc-host-whitelist-enabled": true,
```

Set the **rpc-host-whitelist**, which will be the hostname of the Raspberry Pi and mDNS hostname. the hostname of test Raspberry Pi i am using is **testserver**.

```bash
"rpc-host-whitelist": "testserver, testserver.local",
```

At this point you are done, save the file by pressing **ctrl + x**, press **y** then press **enter**

Reboot the Raspberry Pi

```bash
sudo reboot
```

In order to connect to the Transmission Client, you need to open your web browser and use the mDNS URL or ip address with port 9019.

```bash
http://testserver.local:9091
http://192.168.2.100:9091
```

When you add torrent you can copy and paste the link or magnet link in **enter a Url**. You can also drop and drag one torrent file to the **choice file** button. If you click the **choice file** button, you can at multiple torrent file at once that will be added.

You can delete the torrent or just remove the torrent files and leave the data. If want to delete the file, just right click and choice **Trash Data and Remove from List**. You can remove more then one at once.

## Authors

**Shane Saunders** - *Initial work* - [asparatu](https://github.com/asparatu)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
