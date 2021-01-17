# I/O Scheduling

## Set the I/O scheduling class of your terminal to real time with the highest priority

```
$ sudo ionice --class 1 --classdata 0 -p 2028
$ ionice
realtime: prio 0
```

## Determine which I/O schedulers are available for disk `/dev/sda`

```
$ cat /sys/block/sda/queue/scheduler
noop deadline [cfq]
```

## Change the I/O scheduler for disk `/dev/sda` to a new valid setting

```
# echo deadline > /sys/block/sda/queue/scheduler
# cat /sys/block/sda/queue/scheduler
noop [deadline] cfq
```

## Check whether `/dev/sda` is an SSD or not since this has I/O scheduling algorithm implications

```
# cat /sys/block/sda/queue/rotational
1
```

Thats means the kernel thinks we have a rotational hard disk.