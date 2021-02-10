# Kernel Services and Modules

## Use the appropriate command to list the values of all kernel parameters that can be tuned at runtime

```
$ sudo sysctl -a
```

## Use the filesystem to list the top level categories of tunable kernel parameters

```
$ ls /proc/sys/
```

## Use the filesystem to set `net.ipv4.ip_forward` to 1

```
root@ubuntu-bionic:/proc/sys/net/ipv4# echo 1 > ip_forward
```

## Use appropriate command to set `net.ipv4.ip_forward` to 0

```
/proc/sys/net/ipv4# sysctl -w net.ipv4.ip_forward=0
net.ipv4.ip_forward = 0
/proc/sys/net/ipv4# cat ip_forward
0
```

## Get the value of `kernel.osrelease`

```
$ sysctl kernel.osrelease
kernel.osrelease = 4.15.0-123-generic
```

## Get the limit on the total number of processes? 

```
$ sysctl kernel.threads-max
```

### Although the above is a theoretical limit, what practically speaking limits the process limit?

The stack space used by each process (thread) which you can determine as follows (second command better 
as it shows units):

```
$ ulimit -s
$ ulimit -a
```

- [Useful thread](https://stackoverflow.com/questions/344203/maximum-number-of-threads-per-process-in-linux)
- [Setting limits with ulimit](https://www.networkworld.com/article/2693414/setting-limits-with-ulimit.html)

## What file can be used to set kernel configuration settings at boot time? `cat` the file

```
$ cat /etc/sysctl.conf
```

## How can the above file be used to set all kernel configuration parameters as found at runtime?

```
$ sudo sysctl -p
```

## Run `ps`, check PID, set `kernel.pid_max` to 2000, run `ps` and check PID is now within new range

```
$ ps
  PID TTY          TIME CMD
 1846 pts/0    00:00:00 bash
18159 pts/0    00:00:00 ps
# sysctl -w kernel.pid_max=2000
kernel.pid_max = 2000
root@ubuntu-bionic:/proc/sys# ps
 PID TTY          TIME CMD
 300 pts/0    00:00:00 ps
```