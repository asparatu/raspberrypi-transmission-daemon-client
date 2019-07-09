# How to format a partition in linux

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
