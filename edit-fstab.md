# Edit the fstab file to auto mount the storage

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
