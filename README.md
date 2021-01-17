# LFS201

Linux Foundation Certified System Administrator (LFCS) Preparation

Having completed the _Essentials of Linux System Administration (LFS201)_ course, these are the exercises completed before sitting the actual LFCS exam.

## Setup

As per the Vagrantfile first install these to get a virtual local Ubuntu Ubuntu 18.04.5 LTS:

* vagrant
* virtualbox

Then `vagrant up`, `vagrant ssh`, `cd vagrant` and let's crack on.

## Essential Commands

[Essential Commands](ESSENTIAL_COMMANDS.md)

## Operation of Running System

[Operation of Running System](OPERATION_OF_RUNNING_SYSTEM.md)

## User & Group Management

[User & Group Management](USER_GROUP_MANAGEMENT.md)

## Networking

### find which port/protocol postgresql is meant to use

```
$ grep postgres /etc/services
postgresql	5432/tcp	postgres	# PostgreSQL Database
postgresql	5432/udp	postgres
```

To filter out just the ports:

```
$ grep postgres /etc/services | awk '{print $2}'
5432/tcp
5432/udp
```

### show all network devices

```
$ ip link show
```

### get the `PID/Program name` listening on UDP and TCP ports

Use `sudo` to get the `PID/Program name`. The `ss` command provides richer output.

```
$ sudo netstat --tcp --udp --numeric --listening --program
$ sudo netstat -tunlp
$ sudo ss --tcp --udp --listening --numeric --processes
$ sudo ss tulnp
```

### find which process is listening on port 22

Using the list open files command: `lsof`

```
$ sudo lsof -iTCP:22 -sTCP:LISTEN
```

### find which processes are listening on ssh ports as defined in `/etc/services`

```
$ sudo lsof -iTCP:ssh -sTCP:LISTEN
```

## Storage Management

### Create a virtual loop device `/dev/loop0` using 1GB file `/root/loopbackfile.img` with ext4 filesystem

Do not mount the device (we'll do that in the next step). Note `man losteup` has a handy example.

```
$ sudo dd if=/dev/zero of=/root/loopbackfile.img bs=100M count=10
$ sudo du -sh /root/loopbackfile.img
$ sudo losetup --find --show --partscan /root/loopbackfile.img
$ sudo losetup --all
$ sudo mkfs.ext4 /root/loopbackfile.img
```

### Temporarily mount `/dev/loop0` on `/mnt/backup`, make a `.tar.bz2` file on `/mnt/backup`, then unmount

```
$ sudo mkdir -p /mnt/backup
$ sudo mount -o loop /dev/loop0 /mnt/backup
$ sudo tar -cvjSf tmp.tar.bz2 /tmp/
$ sudo cp tmp.tar.bz2 /mnt/backup/
$ sudo umount /mnt/backup
```

Breaking down the tar commmand:

- `-c` (--create): create a new archive
- `-v` (--verbose:): verbosely list files processed 
- `-j` (--bzip2): filter the archive through bzip2
- `-S` (--sparse): handle sparse files efficiently 
- `-f` (--file): use specified archive file

### Temporarily mount `/dev/loop0` on `/mnt/backup`, decompress and unarchive `/mnt/backup/tmp.tar.bz2` to `/opt`

```
$ sudo tar -xvf /mnt/backup/tmp.tar.bz2 -C /opt
$ sudo mount -o loop /dev/loop0 /mnt/backup
```

### List all partition table devices as mentioned in `/proc/partitions`

```
$ sudo fdisk -l
```

## File System

### Get the file system block size in bytes

The `-l` option lists the contents of the filesystem superblock, including the current values of the parameters 
that can be set via this program.

```
$ sudo tune2fs -l | grep -i 'block size'
Block size:               4096
```

### How much hard drive space is taken up (`devoted`) by a 2 byte file

The minimum amount of file system real estate (hard drive blocks) that can be devoted to a file is 1 block and
so is determined by the file system block size which can be found as follows and in this case it is 4 KB.

```
$ sudo tune2fs -l | grep -i 'block size'
Block size:               4096
``` 

### Explain `du` output on a one character file `echo "1" > geek.txt`

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

### List the apparent sizes on `/home` rather than the disk usage

```
$ sudo du --apparent-size /home -h
``` 

## Processes

### Print the soft limit of the number of open files

```
$ ulimit -a
$ ulimit -n
1024
$ ulimit -S -n
1024
```

### Print the hard limit for the number of open files

```
$ ulimit -H -n
1048576
```

### Increase the open file limit to 1600 for the current shell

```
$ ulimit -n 1600
```

### Modify the system configuration for `compute` group users to a soft limit of 2048

```
$ groups
vagrant compute
$ sudo nano /etc/security/limits.conf
@compute         soft    nofile          2048
$ sudo reboot now
$ ulimit -S -n
2048 
```

### Increase the niceness of your shell by 5

```
$ ps
$ renice +5 -p 1621
```

### Print the shared libraries `bash` depends on

```
$ ldd /bin/bash
	linux-vdso.so.1 (0x00007ffc22fde000)
	libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5 (0x00007f742895b000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f7428757000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7428366000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f7428e9f000)
```

### Send the SIGHUP signal to your own bash terminal

```
$ ps
$ kill -SIGHUP 7920
```

### Kill all of your own processes named `foobar`

```
$ pkill -u vagrant foobar
```

### Make all of your own bash processes run 4 nicer

This relies on `pgrep`.

```
$ renice +4 $(pgrep bash)
8114 (process ID) old priority 0, new priority 4
8239 (process ID) old priority 0, new priority 4
```

## Package Management: dpkg and apt

### Enable source ubuntu supported repositories

Uncomment to suit here. Run `sudo apt-get update` when done modifying `/etc/apt/sources.list`.

```
$ sudo nano /etc/apt/sources.list
$ sudo apt-get update
```

### List all packages installed

```
$ dpkg -l
$ dpkg --list
```

### List all files installed by `wget` package

```
$ dpkg -L wget
$ dpkg --listfiles wget
$ apt-file list wget
```

### Show the status of the `wget` package

```
$ dpkg -s wget
$ dpkg --status wget
```

### Verify the installed `wget` package's integrity

We'll first break the package by deleting a binary, verify then fix it.

```
$ sudo rm /usr/bin/wget
$ dpkg -V wget
??5??????   /usr/bin/wget
$ sudo apt-get remove -f wget
$ sudo apt-get install wget
$ dpkg -V wget; echo $?
0
```

### Uninstall the `wget` package including it's configuration files

`-P` stands for `purge`, the `-r` option removes all of an installed package except it's configuration files.

```
$ sudo dpkg -P wget
$ sudo dpkg -s wget
$ sudo apt-get install wget
$ sudo dpkg -s wget
```

### Find out which package `/etc/logrotate.conf` belongs to

```
$ dpkg -S /etc/logrotate.conf
logrotate: /etc/logrotate.conf
$ dpkg --search /etc/logrotate.conf
logrotate: /etc/logrotate.conf
$ apt-file search /etc/logrotate.conf
logrotate: /etc/logrotate.conf
```

### List all packages that are upgradeable, then upgrade them all

```
$ sudo apt update
$ apt list --upgradeable
$ sudo apt -y upgrade
```

### Perform `apt` clenup operations

`autoremove` is used to remove packages that were automatically installed to
satisfy dependencies for other packages and are now no longer needed as
dependencies changed or the package(s) needing them were removed in the
meantime.

`clean` cleans out cache files and any archived package files that have 
been installed

```
$ sudo apt autoremove
$ sudo apt clean
```

### List all packages with the word `kernel` in their name or description, whether installed or not

Name or description:

```
$ apt-cache search "kernel"
```

Name only:

```
$ apt-cache search --names-only "kernel"
```

### List all installed kernel related packages

On debian based systems the word `linux` is used, not `kernel`.

```
$ dpkg --get-selections "*linux*"
```

### Use `apt` or `apt-cache` to show the package information for `bash`

```
$ apt-cache show bash
```

### Use `apt` or `apt-cache` to show all dependency relationships for `bash`

```
$ apt-cache depends bash
$ apt-cache rdepends bash
```

### Use  `apt` or `apt-cache` to show all available `metapackages`

```
$ apt-cache search metapackage
```

## Monitoring

### Use the `proc` filesystem to check the `threads-max` parameter

```
$ cat /proc/sys/kernel/threads-max
7675
```

### Use the `proc` filesystem to modify the `threads-max` parameter and set it to `100 000`

```
$ sudo bash -c 'echo 100000 > /proc/sys/kernel/threads-max'
vagrant@ubuntu-bionic:/tmp$ cat /proc/sys/kernel/threads-max
100000
```

### Use `sysctl` to modify the `threads-max` parameter and set it to `150 000`

```
$ sudo sysctl kernel.threads-max=150000
kernel.threads-max = 150000
```

### Use the `sys` filesystem to check the available network devices

```
$ ls -l /sys/class/net
```

### Enable `Systems Activity Reporter (SAR) ` data collection using `Systems Activity Data Collector (SADC`

```
$ sudo cat /etc/default/sysstat
#
# Default settings for /etc/init.d/sysstat, /etc/cron.d/sysstat
# and /etc/cron.daily/sysstat files
#

# Should sadc collect system activity informations? Valid values
# are "true" and "false". Please do not put other values, they
# will be overwritten by debconf!
ENABLED="true"
```

Reports are written to log files in:

```
/var/log/sysstat/sa[DD]
```

### List two ways to visually display process ancestry

```
$ ps auxf
$ pstree
```

### Run `ps` so that only the process ID, priority, nice value, and the process command line are displayed

```
$ ps -o pid,pri,nice,cmd
```

### Display the amount of free and used memory in the system

```
$ free -m
```

### Use the `proc` filesystem to display how memory is being used

```
$ cat /proc/meminfo
```

### List the tunable (writable) Virtual Memory system files in the `proc` filesystem

```
$ ls /proc/sys/vm
```

### Use the appropriate tool to report on virtual memory statistics

```
$ vmstat 5 5
```

### Use the `proc` filesystem to allow for all memory requests to overcommit

```
$ cat /proc/sys/vm/overcommit_memory
0
$ sudo bash -c 'echo 1 > /proc/sys/vm/overcommit_memory'
```

### Determine the `oom_score` of an arbitary process using the `proc filesystem`

```
$ cat /proc/835/oom_score
```

### Use the io statistics tool to report on a snapshot of io statistics

```
$ iostat
```

### Continuously report on io statistics

```
$ sudo apt -y install iotop
$ sudo iotop
```

## I/O Scheduling

### Set the I/O scheduling class of your terminal to real time with the highest priority

```
$ sudo ionice --class 1 --classdata 0 -p 2028
$ ionice
realtime: prio 0
```

### Determine which I/O schedulers are available for disk `/dev/sda`

```
$ cat /sys/block/sda/queue/scheduler
noop deadline [cfq]
```

### Change the I/O scheduler for disk `/dev/sda` to a new valid setting

```
# echo deadline > /sys/block/sda/queue/scheduler
# cat /sys/block/sda/queue/scheduler
noop [deadline] cfq
```

### Check whether `/dev/sda` is an SSD or not since this has I/O scheduling algorithm implications

```
# cat /sys/block/sda/queue/rotational
1
```

Thats means the kernel thinks we have a rotational hard disk.

## Virtual Filesystem (VFS) Layer

### Create a file with `touch` and then a hard and soft link to the file, use `ls` to confirm `inode` behaviour

```
$ touch file
$ ln file file-hard
$ ln -s file file-soft
$ ls -li .
2553 -rw-rw-r-- 2 vagrant vagrant    0 Dec 21 19:12 file
2553 -rw-rw-r-- 2 vagrant vagrant    0 Dec 21 19:12 file-hard
2557 lrwxrwxrwx 1 vagrant vagrant    4 Dec 21 19:13 file-soft -> file
```

### Get the current list of supported filesystems

```
$ cat /proc/filesystems
```

### List the partition tables for all devices

```
$ sudo fdisk -l
```

### List all available block devices and include information about their filesystems

```
$ lsblk --fs
```

### Use the `GPT` specific program to print whether `GPT` format is being used on `/dev/sda`

```
$ sudo sgdisk -p /dev/sda

***************************************************************
Found invalid GPT and valid MBR; converting MBR to GPT format
in memory.
***************************************************************
```

### Create a backup of the master boot record (MBR) then confirm that the file is a MBR backup

```
$ sudo dd if=/dev/sda of=mbrbackup bs=512 count=1
$ sudo file mbrbackup
mbrbackup: DOS/MBR boot sector
```

### Use the `GPT` specific program to create a backup of the MBR

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

### List the partitions the OS is currently aware of

```
$ cat /proc/partitions
major minor  #blocks  name

   8        0   10485760 sda
   8        1   10484719 sda1
   8       16      10240 sdb
```