# Monitoring

## Use the `proc` filesystem to check the `threads-max` parameter

```
$ cat /proc/sys/kernel/threads-max
7675
```

## Use the `proc` filesystem to modify the `threads-max` parameter and set it to `100 000`

```
$ sudo bash -c 'echo 100000 > /proc/sys/kernel/threads-max'
vagrant@ubuntu-bionic:/tmp$ cat /proc/sys/kernel/threads-max
100000
```

## Use `sysctl` to modify the `threads-max` parameter and set it to `150 000`

```
$ sudo sysctl kernel.threads-max=150000
kernel.threads-max = 150000
```

## Use the `sys` filesystem to check the available network devices

```
$ ls -l /sys/class/net
```

## Enable `Systems Activity Reporter (SAR) ` data collection using `Systems Activity Data Collector (SADC`

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

## List two ways to visually display process ancestry

```
$ ps auxf
$ pstree
```

## Run `ps` so that only the process ID, priority, nice value, and the process command line are displayed

```
$ ps -o pid,pri,nice,cmd
```

## Display the amount of free and used memory in the system

```
$ free -m
```

## Use the `proc` filesystem to display how memory is being used

```
$ cat /proc/meminfo
```

## List the tunable (writable) Virtual Memory system files in the `proc` filesystem

```
$ ls /proc/sys/vm
```

## Use the appropriate tool to report on virtual memory statistics

```
$ vmstat 5 5
```

## Use the `proc` filesystem to allow for all memory requests to overcommit

```
$ cat /proc/sys/vm/overcommit_memory
0
$ sudo bash -c 'echo 1 > /proc/sys/vm/overcommit_memory'
```

## Determine the `oom_score` of an arbitary process using the `proc filesystem`

```
$ cat /proc/835/oom_score
```

## Use the io statistics tool to report on a snapshot of io statistics

```
$ iostat
```

## Continuously report on io statistics

```
$ sudo apt -y install iotop
$ sudo iotop
```