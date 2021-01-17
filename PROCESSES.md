# Processes

## Print the soft limit of the number of open files

```
$ ulimit -a
$ ulimit -n
1024
$ ulimit -S -n
1024
```

## Print the hard limit for the number of open files

```
$ ulimit -H -n
1048576
```

## Increase the open file limit to 1600 for the current shell

```
$ ulimit -n 1600
```

## Modify the system configuration for `compute` group users to a soft limit of 2048

```
$ groups
vagrant compute
$ sudo nano /etc/security/limits.conf
@compute         soft    nofile          2048
$ sudo reboot now
$ ulimit -S -n
2048 
```

## Increase the niceness of your shell by 5

```
$ ps
$ renice +5 -p 1621
```

## Print the shared libraries `bash` depends on

```
$ ldd /bin/bash
	linux-vdso.so.1 (0x00007ffc22fde000)
	libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5 (0x00007f742895b000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f7428757000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7428366000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f7428e9f000)
```

## Send the SIGHUP signal to your own bash terminal

```
$ ps
$ kill -SIGHUP 7920
```

## Kill all of your own processes named `foobar`

```
$ pkill -u vagrant foobar
```

## Make all of your own bash processes run 4 nicer

This relies on `pgrep`.

```
$ renice +4 $(pgrep bash)
8114 (process ID) old priority 0, new priority 4
8239 (process ID) old priority 0, new priority 4
```