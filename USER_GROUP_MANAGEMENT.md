# User & Group Management

## folder group ownership

- Add a compute group
- Add a /exam/compute folder (both at the same time)
- Make the compute group the owner of /exam/compute

```
$ sudo mkdir -p /exam/compute
$ sudo addgroup compute
$ sudo chown -R :compute /exam/compute
```

## add user with password and sudo without password promopt

```
sudo useradd candidate
sudo usermod candidate -p cert456
sudo nano /etc/sudoers.d/vagrant
$ sudo cat /etc/sudoers.d/vagrant
vagrant ALL=(ALL) NOPASSWD:ALL
candidate ALL=(ALL) NOPASSWD:ALL
```

## empty NEWS file auto create in home dir of any new user

```
$ sudo touch /etc/skel/NEWS
$ sudo nano /etc/default/useradd
SKEL=/etc/skel
$ sudo useradd --create-home linda
```

## create a group called students

```
$ sudo groupadd students
```

## create particular new user

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

## create a particular sysadmin user

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

## all users must be able to execute `last` command

The last command shows a listing of last logged in users.

```
$ sudo chmod a+x  /usr/bin/last
```

## make changes to `zak` user

- password to: `_foo123_`
- home dir to: /home/zak
- shell to: zsh

```
$ sudo usermod zak -d /home/zak -m -s /usr/bin/zsh
$ sudo passwd zak
```

## create user `foo` that has a password but cannot log into the system

This must be a user that can later log in, and so not a system user and it can have a home dir.

```
$ sudo usermod foo -s /usr/sbin/nologin 
$ sudo passwd foo
```

## alter the `foo` user so that it can log into the system

The user's shell goes from `/usr/sbin/nologin` to `/bin/bash`.

```
$ sudo usermod foo -s /bin/bash
```