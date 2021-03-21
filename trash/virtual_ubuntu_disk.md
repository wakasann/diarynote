```
df -hl
```



```
Disk /dev/sdb: 13.2 GiB, 14220304384 bytes, 27774032 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x21a4affc

Device     Boot Start      End  Sectors  Size Id Type
/dev/sdb1        2048 27774031 27771984 13.2G  5 Extended
```



```
waka@ubuntus1:~$ sudo mkfs -t ext4 /dev/sdb1 #格式化磁盤
mke2fs 1.42.13 (17-May-2015)
Found a dos partition table in /dev/sdb1
Proceed anyway? (y,n) y
mkfs.ext4: inode_size (128) * inodes_count (0) too big for a
	filesystem with 0 blocks, specify higher inode_ratio (-i)
	or lower inode count (-N).
```

因爲 `/dev/sdb1`的類型是`Extended`，通過`fdisk /dev/sdb`命令，先删掉这个分区，然后再添加一个logical或者primary的分区

> 方法`fdisk /dev/sdb` -> `d`-> `n`-> `p`(或者l) ->`1`(或其他数字)-> `w`(写入)

```
waka@ubuntus1:~$ sudo fdisk /dev/sdb
Command (m for help): p
Disk /dev/sdb: 13.2 GiB, 14220304384 bytes, 27774032 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x21a4affc

Device     Boot Start      End  Sectors  Size Id Type
/dev/sdb1        2048 27774031 27771984 13.2G  5 Extended
Command (m for help): d
Selected partition 1
Partition 1 has been deleted.
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-27774031, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-27774031, default 27774031): 

Created a new partition 1 of type 'Linux' and of size 13.2 GiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

再格式化磁盤分區就可以了

```
waka@ubuntus1:~$ sudo mkfs -t ext4 /dev/sdb1
mke2fs 1.42.13 (17-May-2015)
Found a dos partition table in /dev/sdb1
Proceed anyway? (y,n) y
Creating filesystem with 3471498 4k blocks and 868352 inodes
Filesystem UUID: cf5124de-c853-4b18-a9bd-ea0cfafb7918
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done   
```

將新建的`/dev/sdb1`掛載到`/home`下

```
sudo mount /dev/sdb1 /home
```



通過參考鏈接的`dd`命令，將虛擬機中的`ubuntu`的磁盤空間寫滿

執行了2次下面的命令之後，磁盤就滿了

```
sudo dd if=/dev/zero of=/tmp/file bs=2G count=10
```



```
waka@ubuntus1:~$ df -hl
Filesystem                     Size  Used Avail Use% Mounted on
udev                           980M     0  980M   0% /dev
tmpfs                          201M  3.3M  197M   2% /run
/dev/mapper/ubuntus1--vg-root  8.1G  7.8G     0 100% /
tmpfs                         1001M     0 1001M   0% /dev/shm
tmpfs                          5.0M     0  5.0M   0% /run/lock
tmpfs                         1001M     0 1001M   0% /sys/fs/cgroup
/dev/sda1                      720M   58M  625M   9% /boot
tmpfs                          201M     0  201M   0% /run/user/1000
/dev/sdb1                       13G   34M   13G   1% /home
```



```
waka@ubuntus1:~$ df -hl
Filesystem                     Size  Used Avail Use% Mounted on
udev                           980M     0  980M   0% /dev
tmpfs                          201M  3.2M  197M   2% /run
/dev/mapper/ubuntus1--vg-root  8.1G  1.8G  5.9G  24% /
tmpfs                         1001M     0 1001M   0% /dev/shm
tmpfs                          5.0M     0  5.0M   0% /run/lock
tmpfs                         1001M     0 1001M   0% /sys/fs/cgroup
/dev/sda1                      720M   58M  625M   9% /boot
tmpfs                          201M     0  201M   0% /run/user/1000
waka@ubuntus1:~$ sudo fdisk -l
[sudo] password for waka: 
Disk /dev/sda: 78.2 GiB, 83921010688 bytes, 163908224 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048  1499135  1497088  731M 83 Linux
/dev/sda2       1501182 20969471 19468290  9.3G  5 Extended
/dev/sda5       1501184 20969471 19468288  9.3G 8e Linux LVM


Disk /dev/mapper/ubuntus1--vg-root: 8.3 GiB, 8938061824 bytes, 17457152 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/ubuntus1--vg-swap_1: 980 MiB, 1027604480 bytes, 2007040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
waka@ubuntus1:~$ sudo fdisk /dev/sda

Welcome to fdisk (util-linux 2.27.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/sda: 78.2 GiB, 83921010688 bytes, 163908224 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048  1499135  1497088  731M 83 Linux
/dev/sda2       1501182 20969471 19468290  9.3G  5 Extended
/dev/sda5       1501184 20969471 19468288  9.3G 8e Linux LVM

Command (m for help): sudo ^H^H
Created a new partition 1 of type 'Linux native' and of size 78.1 GiB.
Created a new partition 2 of type 'Linux swap' and of size 47.1 MiB.
Created a new partition 3 of type 'Whole disk' and of size 78.2 GiB.
Created a new Sun disklabel.

Command (m for help): q

waka@ubuntus1:~$ sudo bash
root@ubuntus1:~# free -m
              total        used        free      shared  buff/cache   available
Mem:           2000         170        1637           3         192        1675
Swap:           979           0         979
root@ubuntus1:~# fdisk /dev/sda

Welcome to fdisk (util-linux 2.27.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/sda: 78.2 GiB, 83921010688 bytes, 163908224 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048  1499135  1497088  731M 83 Linux
/dev/sda2       1501182 20969471 19468290  9.3G  5 Extended
/dev/sda5       1501184 20969471 19468288  9.3G 8e Linux LVM

Command (m for help): d1
Partition number (1,2,5, default 5): 1

Partition 1 has been deleted.

Command (m for help): q

root@ubuntus1:~# fdisk /dev/sda

Welcome to fdisk (util-linux 2.27.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/sda: 78.2 GiB, 83921010688 bytes, 163908224 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048  1499135  1497088  731M 83 Linux
/dev/sda2       1501182 20969471 19468290  9.3G  5 Extended
/dev/sda5       1501184 20969471 19468288  9.3G 8e Linux LVM

Command (m for help): d
Partition number (1,2,5, default 5): 

Partition 5 has been deleted.

Command (m for help): ^[[A
[: unknown command

Command (m for help): d
Partition number (1,2, default 2): 

Partition 2 has been deleted.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): e
Partition number (2-4, default 2): 
First sector (1499136-163908223, default 1499136): 1501182
Last sector, +sectors or +size{K,M,G,T,P} (1501182-163908223, default 163908223): 20969471

Created a new partition 2 of type 'Extended' and of size 9.3 GiB.

Command (m for help): p
Disk /dev/sda: 78.2 GiB, 83921010688 bytes, 163908224 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048  1499135  1497088  731M 83 Linux
/dev/sda2       1501182 20969471 19468290  9.3G  5 Extended

Command (m for help): n
Partition type
   p   primary (1 primary, 1 extended, 2 free)
   l   logical (numbered from 5)
Select (default p): p
Partition number (3,4, default 3): 
First sector (1499136-163908223, default 1499136): 1501184

Sector 1501184 is already allocated.
First sector (20969472-163908223, default 20969472): 
Last sector, +sectors or +size{K,M,G,T,P} (20969472-163908223, default 163908223): 

Created a new partition 3 of type 'Linux' and of size 68.2 GiB.

Command (m for help): p
Disk /dev/sda: 78.2 GiB, 83921010688 bytes, 163908224 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Device     Boot    Start       End   Sectors  Size Id Type
/dev/sda1  *        2048   1499135   1497088  731M 83 Linux
/dev/sda2        1501182  20969471  19468290  9.3G  5 Extended
/dev/sda3       20969472 163908223 142938752 68.2G 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Re-reading the partition table failed.: Device or resource busy

The kernel still uses the old table. The new table will be used at the next reboot or after you run partprobe(8) or kpartx(8).

```



重啓之後

![uploads/200516/fs2020051600009a.jpg](uploads/200516/fs2020051600009a.jpg)





```
root@ubuntus1:~# sudo fdisk /dev/sda

Welcome to fdisk (util-linux 2.27.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/sda: 61.9 GiB, 66428796928 bytes, 129743744 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sda1  *       2048  1499135  1497088  731M 83 Linux
/dev/sda2       1501182 20969471 19468290  9.3G  5 Extended
/dev/sda5       1501184 20969471 19468288  9.3G 8e Linux LVM

Command (m for help): d
Partition number (1,2,5, default 5): 1

Partition 1 has been deleted.

Command (m for help): d
Partition number (2,5, default 5): 2

Partition 2 has been deleted.

Command (m for help): p
Disk /dev/sda: 61.9 GiB, 66428796928 bytes, 129743744 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-129743743, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-129743743, default 129743743): 1497088

Created a new partition 1 of type 'Linux' and of size 730 MiB.

Command (m for help): p
Disk /dev/sda: 61.9 GiB, 66428796928 bytes, 129743744 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Device     Boot Start     End Sectors  Size Id Type
/dev/sda1        2048 1497088 1495041  730M 83 Linux

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 2
First sector (1497089-129743743, default 1499136): 
Last sector, +sectors or +size{K,M,G,T,P} (1499136-129743743, default 129743743): 

Created a new partition 2 of type 'Linux' and of size 61.2 GiB.

Command (m for help): p
Disk /dev/sda: 61.9 GiB, 66428796928 bytes, 129743744 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x7e2bb634

Device     Boot   Start       End   Sectors  Size Id Type
/dev/sda1          2048   1497088   1495041  730M 83 Linux
/dev/sda2       1499136 129743743 128244608 61.2G 83 Linux

Command (m for help): t
Partition number (1,2, default 2): 2
Partition type (type L to list all types): 8e

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Re-reading the partition table failed.: Device or resource busy

The kernel still uses the old table. The new table will be used at the next reboot or after you run partprobe(8) or kpartx(8).
```





## References

1. [linux格式化磁盘出错](https://www.jianshu.com/p/8b177adb2ba9)
2. [如何快速将磁盘写满！！](https://blog.csdn.net/qq_35523593/article/details/78668550)