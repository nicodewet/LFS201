# Package Management: dpkg and apt

## Enable source ubuntu supported repositories

Uncomment to suit here. Run `sudo apt-get update` when done modifying `/etc/apt/sources.list`.

```
$ sudo nano /etc/apt/sources.list
$ sudo apt-get update
```

## List all packages installed

```
$ dpkg -l
$ dpkg --list
```

## List all files installed by `wget` package

```
$ dpkg -L wget
$ dpkg --listfiles wget
$ apt-file list wget
```

## Show the status of the `wget` package

```
$ dpkg -s wget
$ dpkg --status wget
```

## Verify the installed `wget` package's integrity

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

## Uninstall the `wget` package including it's configuration files

`-P` stands for `purge`, the `-r` option removes all of an installed package except it's configuration files.

```
$ sudo dpkg -P wget
$ sudo dpkg -s wget
$ sudo apt-get install wget
$ sudo dpkg -s wget
```

## Find out which package `/etc/logrotate.conf` belongs to

```
$ dpkg -S /etc/logrotate.conf
logrotate: /etc/logrotate.conf
$ dpkg --search /etc/logrotate.conf
logrotate: /etc/logrotate.conf
$ apt-file search /etc/logrotate.conf
logrotate: /etc/logrotate.conf
```

## List all packages that are upgradeable, then upgrade them all

```
$ sudo apt update
$ apt list --upgradeable
$ sudo apt -y upgrade
```

## Perform `apt` clenup operations

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

## List all packages with the word `kernel` in their name or description, whether installed or not

Name or description:

```
$ apt-cache search "kernel"
```

Name only:

```
$ apt-cache search --names-only "kernel"
```

## List all installed kernel related packages

On debian based systems the word `linux` is used, not `kernel`.

```
$ dpkg --get-selections "*linux*"
```

## Use `apt` or `apt-cache` to show the package information for `bash`

```
$ apt-cache show bash
```

## Use `apt` or `apt-cache` to show all dependency relationships for `bash`

```
$ apt-cache depends bash
$ apt-cache rdepends bash
```

## Use  `apt` or `apt-cache` to show all available `metapackages`

```
$ apt-cache search metapackage
```