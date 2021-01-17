# Networking

## find which port/protocol postgresql is meant to use

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

## show all network devices

```
$ ip link show
```

## get the `PID/Program name` listening on UDP and TCP ports

Use `sudo` to get the `PID/Program name`. The `ss` command provides richer output.

```
$ sudo netstat --tcp --udp --numeric --listening --program
$ sudo netstat -tunlp
$ sudo ss --tcp --udp --listening --numeric --processes
$ sudo ss tulnp
```

## find which process is listening on port 22

Using the list open files command: `lsof`

```
$ sudo lsof -iTCP:22 -sTCP:LISTEN
```

## find which processes are listening on ssh ports as defined in `/etc/services`

```
$ sudo lsof -iTCP:ssh -sTCP:LISTEN
```