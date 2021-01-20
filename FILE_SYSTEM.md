# File System

## Get the file system block size in bytes

The `-l` option lists the contents of the filesystem superblock, including the current values of the parameters 
that can be set via this program.

```
$ sudo tune2fs -l | grep -i 'block size'
Block size:               4096
```

## How much hard drive space is taken up (`devoted`) by a 2 byte file

The minimum amount of file system real estate (hard drive blocks) that can be devoted to a file is 1 block and
so is determined by the file system block size which can be found as follows and in this case it is 4 KB.

```
$ sudo tune2fs -l | grep -i 'block size'
Block size:               4096
``` 

## Explain `du` output on a one character file `echo "1" > geek.txt`

See [How to Get the Size of a File of Directory in Linux](https://www.howtogeek.com/450366/how-to-get-the-size-of-a-file-or-directory-in-linux/) as background.

```
$ echo "1" > geek.txt
$ hexdump -C geek.txt
00000000  31 0a                                             |1.|
00000002
$ du geek.txt
4	geek.txt
$ sudo tune2fs -l /dev/sda1 | grep -i 'block size'
Block size:               4096
```

From the above we know geek.txt is composed of 2 bytes for `1` and a line feed character. Because we know the default block size
is `4096` bytes the `du geek.txt` output must mean `du` is using the default assumption of 1024 bytes per block and so reports
that we have `4` 1024 byte blocks for geek.txt which matches the minimum block size of `4096` bytes.

## List the apparent sizes on `/home` rather than the disk usage

```
$ sudo du --apparent-size /home -h
``` 

## Change the file `geek.txt` to be append only, check the extended attributes of the file before and after

```
$ lsattr geek.txt
--------------e--- geek.txt
vagrant@ubuntu-bionic:~$ sudo chattr +a geek.txt
vagrant@ubuntu-bionic:~$ lsattr geek.txt
-----a--------e--- geek.txt
```

## Change all files in `/home/vagrant` so that the secure deletion extended attribute of all files is set

```
$ chattr -R +s /home/vagrant
```

## Configure the system to force a filesystem error check and repair on reboot including on the root filesystem, then reboot

```
$ sudo touch /forcefsck
$ sudo reboot
```

## List the currently mounted filesystems

```
$ mount
```

## Display the PIDs of processes using the `/home` directory

```
$ fuser -m /home
/home:                3374rce  3471rce
```

## Provide detailed ext2/ext3/ext4 filesystem information on `/dev/sda1` or any relevant device

```
$ sudo dumpe2fs /dev/sda1 | less
```

## Check if your system is able to support filesystem quotas on any filesystem

Here we are checking if any filesystem has been mounted with the `usrquota` or `grpquota` mount options since without this linux
filesystem quota enforcement will not work.

```
$ egrep 'usrquota|grpquota' /etc/fstab
```

## List the filesystem disk space usage in human readable format and by type

Note `df` stands for disk free.

```
$ df -hT
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  480M     0  480M   0% /dev
tmpfs          tmpfs      99M  600K   98M   1% /run
/dev/sda1      ext4      9.7G  2.0G  7.8G  20% /
tmpfs          tmpfs     493M     0  493M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     493M     0  493M   0% /sys/fs/cgroup
vagrant        vboxsf    113G   88G   26G  78% /vagrant
tmpfs          tmpfs      99M     0   99M   0% /run/user/1000
```

## Get the disk usage of the `\bin` directory at a top level

```
$ sudo du -d0 -h /bin
```

## Print ALL the super block and block group information for the filesystem present on the `/dev/sda1` device

`-l     List the contents of the filesystem superblock, including the current values of the parameters that can be set via this program.`

```
$ sudo tune2fs -l /dev/sda1
```

## List the contents of the ext4 superblock, including the current values of parameters which can be changed

```
$ sudo tune2fs -l /dev/sda1
```

## Confirm that the max-mount-count of ext4 filesystem is at the default which is -1

`If max-mount-counts is 0 or -1, the number of times the filesystem is mounted will be disregarded
              by e2fsck(8) and the kernel.
Mount-count-dependent checking is disabled by default to avoid unanticipated long reboots while e2fsck does its work. However, you may wish to consider the consequences of disabling mount-count-dependent checking entirely. 
Bad disk drives, cables, memory, and kernel bugs could all corrupt a filesystem without marking the filesystem dirty or in error. If you are using journaling on your filesystem, your filesystem will never be marked dirty, so it will not normally be checked. A filesystem error detected by the kernel will still force an fsck on the next reboot, but it may already be too late to prevent data loss at that point.
`

```
$ sudo tune2fs -l /dev/sda1 | grep 'Maximum mount count'
Maximum mount count:      -1
```

## Change the maximum number of mounts between ext4 filesystem checks (max-mount-count) to 25

```
$ sudo tune2fs -c 25 /dev/sda1
tune2fs 1.44.1 (24-Mar-2018)
Setting maximal mount count to 25
```

## Change the time interval between ext4 filesystem checks (interval-between-checks) to 10 days

`
-i  interval-between-checks[d|m|w]
Adjust the maximal time between two filesystem checks.  No suffix or d will interpret the number interval-between-checks as days, m as months, and w as weeks. A value of zero will disable the 
time-dependent checking. There are pros and cons to disabling these periodic checks; see the 
discussion under the -c (mount-count-dependent check) option for details.
`

```
$ sudo tune2fs -i 10d /dev/sda1
tune2fs 1.44.1 (24-Mar-2018)
Setting interval between checks to 864000 seconds
```

