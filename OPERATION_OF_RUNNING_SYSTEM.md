# Operation of Running System

See [this resource for AWK background](https://www.grymoire.com/Unix/Awk.html).

## Basic bash script: invoking user and default gateway IP

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

## Basic bash script: print file name and owner from ls -l command

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

## Basic script: print all user names that do not have a password

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

## Install the tmux package

```
$ sudo apt install tmux
```

## Cron job

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