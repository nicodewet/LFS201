# Virtual Filesystem (VFS) Layer

## Create a file with `touch` and then a hard and soft link to the file, use `ls` to confirm `inode` behaviour

```
$ touch file
$ ln file file-hard
$ ln -s file file-soft
$ ls -li .
2553 -rw-rw-r-- 2 vagrant vagrant    0 Dec 21 19:12 file
2553 -rw-rw-r-- 2 vagrant vagrant    0 Dec 21 19:12 file-hard
2557 lrwxrwxrwx 1 vagrant vagrant    4 Dec 21 19:13 file-soft -> file
```

## Get the current list of supported filesystems

```
$ cat /proc/filesystems
```

## List the partition tables for all devices

```
$ sudo fdisk -l
```

## List all available block devices and include information about their filesystems

```
$ lsblk --fs
```

## Use the `GPT` specific program to print whether `GPT` format is being used on `/dev/sda`

```
$ sudo sgdisk -p /dev/sda

***************************************************************
Found invalid GPT and valid MBR; converting MBR to GPT format
in memory.
***************************************************************
```

## Create a backup of the master boot record (MBR) then confirm that the file is a MBR backup

```
$ sudo dd if=/dev/sda of=mbrbackup bs=512 count=1
$ sudo file mbrbackup
mbrbackup: DOS/MBR boot sector
```

## Use the `GPT` specific program to create a backup of the MBR

```
$ sudo sgdisk --backup mbrbackup2 /dev/sda

***************************************************************
Found invalid GPT and valid MBR; converting MBR to GPT format
in memory.
***************************************************************

The operation has completed successfully.
$ file mbrbackup2
mbrbackup2: DOS/MBR boot sector
```

## List the partitions the OS is currently aware of

```
$ cat /proc/partitions
major minor  #blocks  name

   8        0   10485760 sda
   8        1   10484719 sda1
   8       16      10240 sdb
```