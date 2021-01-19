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