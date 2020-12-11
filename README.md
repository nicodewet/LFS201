# LFS201

Linux Foundation Certified System Administrator (LFCS) Preparation

Having completed the _Essentials of Linux System Administration (LFS201)_ course, these are the exercises completed before sitting the actual LFCS exam.

## Setup

As per the Vagrantfile first install these to get a virtual local Ubuntu Ubuntu 18.04.5 LTS:

* vagrant
* virtualbox

Then `vagrant up`, `vagrant ssh`, `cd vagrant` and let's crack on.

## Cracking On

### Essential Commands

#### Count the number of lines in lorem.txt

```
/vagrant$ wc -l lorem.txt 
209 lorem.txt
```

Assuming lines are numbered from 1 to 210, move the 210th line to line 1.

```
/vagrant$ ed -p% lorem.txt 
Newline appended
60155
%n
210	Consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus.
%210m0
%n
1	Consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus.
%w
60155
%q
```

#### Delete lines 208 to 210 inclusive.

```
/vagrant$ ed -p% lorem.txt 
60155
%n
210	
%208,210d
%n
207	Duis autem vel eum iriure dolor in hendrerit in vulputate velit esse molestie consequat, vel illum dolore eu feugiat nulla facilisis.   
%wq
59281
```

#### Replace every occurrence of the word Consetetur with an uppercase C with the word Cricket.

```
/vagrant$ sed -i 's/Consetetur/Cricket/g' lorem.txt
```

s is the substitute command for sed to find and replace.
g means substitute globally (without this option the first occurance will be replaced).
To find and replace in case insensitive manner we would use the I option i.e. 's/Consetetur/Cricket/gI'
-i means update the file.


#### Add a new line at the very end of the document that contains _Lorem ipsum_.

```
/vagrant$ printf '\nLorem ipsum' >> lorem.txt
```

#### Archive and compression exercises

- Create a /opt/tmp001.zip file out of /tmp
- Extract all files from archive file /opt/tmp001.zip into target directory /opt/SAMPLE001
- Create a tar archive file /opt/SAMPLE0001.tar containing the files in the directory /opt/SAMPLE001
- Compress the archive file /opt/SAMPLE0001.tar using the bzip2 compression algorithm 
- Compress the tar archive file /opt/SAMPLE0001.tar using the `xz` compression algorithm

```
$ zip -r tmp001.zip /tmp/
$ sudo mv tmp001.zip /opt/
$ sudo unzip /opt/tmp001.zip -d /opt/SAMPLE001
$ sudo tar -cvf /opt/SAMPLE0001.tar /opt/SAMPLE001/
$ sudo xz -k /opt/SAMPLE0001.tar
``` 

#### Finding files exercises

See [40 Best Examples of Find command in Linux](https://geekflare.com/linux-find-commands/) as a reference.

##### Find all directories in `/vagrant/FILES`.

```
$ find /vagrant/FIND -type d
/vagrant/FIND
/vagrant/FIND/empty
/vagrant/FIND/somedir
/vagrant/FIND/another
```

##### Find all files in `/vagrant/FILES` (result not shown for brevity), then count them.

```
$ find /vagrant/FIND -type f
$ find /vagrant/FIND -type f | wc -l
14
```

##### Find all files in `/vagrant/FIND` with case-insensitive word `heythere` in title.

```
$ find /vagrant/FIND -type f -iname heythere*
/vagrant/FIND/somedir/HEYthERE1.sh
/vagrant/FIND/somedir/heythere.sh
```

##### Find all executable files in `/vagrant/FIND`

```
$ find /vagrant/FIND -type f -executable
/vagrant/FIND/exec2.sh
/vagrant/FIND/exec1.sh
/vagrant/FIND/somedir/surprise.sh
```

##### Find all files in `/vagrant/FIND` containing the case sensitive word `kiwi`. 

The `-l` option in `grep` means `--files-with-matches` which will suppress normal output and instead print the name of each input file from which
output would normally be printed.

Note with `find (GNU findutils) 4.7.0-git` the `;` needs to be escaped else you'll get `find: missing argument to -exec`.

```
$ find /vagrant/FIND -type f -exec grep -l "kiwi" {} \;
/vagrant/FIND/hey
/vagrant/FIND/bob
```

##### Find all files in `/vagrant/FIND` larger than 512kb in size

```
$ find /vagrant/FIND -type f -size +512k
/vagrant/FIND/somedir/ddgenfile.img
```

Even better include the actual file size in human readable format as follows.

```
$ find /vagrant/FIND -type f -size +512k -exec du -h {} \;
1.0M	/vagrant/FIND/somedir/ddgenfile.img
```

##### Find all empty files in `/vagrant/FIND`

```
$ find /vagrant/FIND/ -type f -empty
```

To sanity check:

```
$ find /vagrant/FIND/ -type f -empty -exec du {} \;
```

#### Find all directories in `/vagrant/FIND` larger than 20kb

```
$ find /vagrant/FIND -type d -size +20k
```

##### Find all files in `/vagrant/FIND` that have not been accessed in the last 30 days

```
$ find /vagrant/FIND -type f -atime +30
```

##### Find all files in `/vagrant/FIND` that have not been modified in the last 30 days

```
$ find /vagrant/FIND -type f -mtime +30
```

##### Find all executable files in `/vagrant/FIND` and delete them

```
$ find /vagrant/FIND -type f -executable
$ find /vagrant/FIND -type f -executable -exec rm {} \;
$ find /vagrant/FIND -type f -executable
```

##### Find all empty files in `/vagrant/FIND` and delete them

```
$ find /vagrant/FIND -type f -empty
$ find /vagrant/FIND -type f -empty -exec rm {} \;
$ find /vagrant/FIND -type f -empty
```

##### Find all files in `/vagrant/FIND` with `.txt` extension and write with full path to `/vagrant/FIND/compress.txt`

```
$ find /vagrant/FIND -type f -name "*.txt"
$ find /vagrant/FIND -type f -name "*.txt" > /vagrant/FIND/compress.txt
```

##### Find all files containing the case insensitive word `kiwi`.

The `-i` means ignore case distinctions, so that characters that differ only in case match each other.

```
$ find /vagrant/FIND -type f -exec grep -l -i "kiwi" {} \;
$ find /vagrant/FIND -type f -exec grep --files-with-matches  --ignore-case "kiwi" {} \;
/vagrant/FIND/super.txt
/vagrant/FIND/hey
/vagrant/FIND/bob
```

### Operation of Running System

See [this resource for AWK background](https://www.grymoire.com/Unix/Awk.html).

#### Basic bash script: invoking user and default gateway IP

Create script /vagrant/test.sh which must:
* be invokeable as ./test.sh
* first line of output from script must be name of invoking user
* second line of output from script must be the IP address of the default gateway

```
$ touch GatewayIP.sh
$ chmod u+x GatewayIP.sh
$ nano GatewayIP.sh
#!/usr/bin/env bash
echo $(whoami)
route -n | awk '{if ($4=="UG") print $2}'
```

#### Basic bash script: print file name and owner from ls -l command

Preparation.

```
$ mkdir ~/.local/bin
$ cat .bash_profile 
export PATH="~/.local/bin:$PATH"
$ touch ~/.local/bin/FileOwner.awk
$ chmod u+x ~/.local/bin/FileOwner.awk
$ source .bash_profile
```

`~/.local/bin/FileOwner.sh` uses AWK since it is well suited to processing the rows and columns returned by many UNIX utilities.

```
#!/usr/bin/awk -f
BEGIN { print "File\tOwner" }
{ print $9, "\t", $3}
END { print " - DONE -" }
```

Now use the script as follows:

```
$ ls -l | FileOwner.awk
```

#### Basic script: print all user names that do not have a password

This solution uses AWK's ability to process files that do not use whitespace as a separator. We use the AWK _FS Input Field Seperator Variable_ to set the input field seperator.

```
$ touch ~/.local/bin/nopasswd.awk
$ chmod u+x ~/.local/bin/nopasswd.awk
#!/usr/bin/awk -f
BEGIN
{
  FS=":";
}
{
  if ( $2 == "") {
      print $1 ": no password";
  }
}
```

#### Install the tmux package

```
$ sudo apt install tmux
```

#### Cron job

Create a cron job that kills all processes named `scan_filesystem` which is owned by `root`, every minute.

Use `PIDS=$(pgrep -u root scan_filesystem)` to get the space seperated set of PIDs named `scan_filesystem` which is owned by `root`.

Create a file /root/killer.sh

```
PIDS=$(pgrep -u root scan_filesystem)
for pid in $PIDS; do kill $pid; done
```

Now `crontab -e` and add this:
```
* * * * * /root/killer.sh
```

Now `tail -f /var/log/syslog`

Now as a test do this and you should see the process terminated (do it in two windows):
```
# ln /bin/sleep /bin/scan_filesystem
# scan_filesystem 5m
Terminated
``` 

### User & Group Management

#### folder group ownership

- Add a compute group
- Add a /exam/compute folder (both at the same time)
- Make the compute group the owner of /exam/compute

```
$ sudo mkdir -p /exam/compute
$ sudo addgroup compute
$ sudo chown -R :compute /exam/compute
```

#### add user with password and sudo without password promopt

```
sudo useradd candidate
sudo usermod candidate -p cert456
sudo nano /etc/sudoers.d/vagrant
$ sudo cat /etc/sudoers.d/vagrant
vagrant ALL=(ALL) NOPASSWD:ALL
candidate ALL=(ALL) NOPASSWD:ALL
```

#### empty NEWS file auto create in home dir of any new user

```
$ sudo touch /etc/skel/NEWS
$ sudo nano /etc/default/useradd
SKEL=/etc/skel
$ sudo useradd --create-home linda
```

#### create a group called students

```
$ sudo groupadd students
```

#### create particular new user

- username `zak`
- password `magic`
- home dir `/home/school/zak`
- member of `students` group
- `/home/school/zak/binaries/` dir is part of the `PATH` variable

```
$ sudo mkdir /home/school
$ sudo useradd zak -b /home/school -G students -m
$ sudo passwd zak
$ sudo touch /home/school/zak/.bash_profile
PATH=$PATH:/home/school/zak/binaries/
$ sudo chown zak:zak /home/school/zak/binaries
```

#### create a particular sysadmin user

- username `sysadmin`
- password `science`
- home dir `/sysadmin/`
- sudoer and no password prompt when using sudo
- default shell is zsh

```
$ sudo apt install zsh
$ sudo useradd sysadmin -d /sysadmin -m -s /usr/bin/zsh
$ sudo passwd sysadmin
$ sudo nano /etc/sudoers.d/vagrant
$ sudo cat /etc/sudoers.d/vagrant
vagrant ALL=(ALL) NOPASSWD:ALL
candidate ALL=(ALL) NOPASSWD:ALL
sysadmin ALL=(ALL) NOPASSWD:ALL
```

#### all users must be able to execute `last` command

The last command shows a listing of last logged in users.

```
$ sudo chmod a+x  /usr/bin/last
```

#### make changes to `zak` user

- password to: `_foo123_`
- home dir to: /home/zak
- shell to: zsh

```
$ sudo usermod zak -d /home/zak -m -s /usr/bin/zsh
$ sudo passwd zak
```

#### create user `foo` that has a password but cannot log into the system

This must be a user that can later log in, and so not a system user and it can have a home dir.

```
$ sudo usermod foo -s /usr/sbin/nologin 
$ sudo passwd foo
```

#### alter the `foo` user so that it can log into the system

The user's shell goes from `/usr/sbin/nologin` to `/bin/bash`.

```
$ sudo usermod foo -s /bin/bash
```

### Networking

#### find which port/protocol postgresql is meant to use

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

#### show all network devices

```
$ ip link show
```

#### get the `PID/Program name` listening on UDP and TCP ports

Use `sudo` to get the `PID/Program name`. The `ss` command provides richer output.

```
$ sudo netstat --tcp --udp --numeric --listening --program
$ sudo netstat -tunlp
$ sudo ss --tcp --udp --listening --numeric --processes
$ sudo ss tulnp
```

#### find which process is listening on port 22

Using the list open files command: `lsof`

```
$ sudo lsof -iTCP:22 -sTCP:LISTEN
```

#### find which processes are listening on ssh ports as defined in `/etc/services`

```
$ sudo lsof -iTCP:ssh -sTCP:LISTEN
```

### Storage Management

#### Create a virtual loop device `/dev/loop0` using 1GB file `/root/loopbackfile.img` with ext4 filesystem

Do not mount the device (we'll do that in the next step). Note `man losteup` has a handy example.

```
$ sudo dd if=/dev/zero of=/root/loopbackfile.img bs=100M count=10
$ sudo du -sh /root/loopbackfile.img
$ sudo losetup --find --show --partscan /root/loopbackfile.img
$ sudo losetup --all
$ sudo mkfs.ext4 /root/loopbackfile.img
```

#### Temporarily mount `/dev/loop0` on `/mnt/backup`, make a `.tar.bz2` file on `/mnt/backup`, then unmount

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

#### Temporarily mount `/dev/loop0` on `/mnt/backup`, decompress and unarchive `/mnt/backup/tmp.tar.bz2` to `/opt`

```
$ sudo tar -xvf /mnt/backup/tmp.tar.bz2 -C /opt
$ sudo mount -o loop /dev/loop0 /mnt/backup
```

#### List all partition table devices as mentioned in `/proc/partitions`

```
$ sudo fdisk -l
```

### File System

#### Get the file system block size in bytes

The `-l` option lists the contents of the filesystem superblock, including the current values of the parameters 
that can be set via this program.

```
$ sudo tune2fs -l | grep -i 'block size'
Block size:               4096
```

#### How much hard drive space is taken up (`devoted`) by a 2 byte file

The minimum amount of file system real estate (hard drive blocks) that can be devoted to a file is 1 block and
so is determined by the file system block size which can be found as follows and in this case it is 4 KB.

```
$ sudo tune2fs -l | grep -i 'block size'
Block size:               4096
``` 

#### Explain `du` output on a one character file `echo "1" > geek.txt`

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

#### List the apparent sizes on `/home` rather than the disk usage

```
$ sudo du --apparent-size /home -h
``` 
