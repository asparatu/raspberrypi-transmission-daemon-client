# How to create a Linux partition

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
