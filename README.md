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