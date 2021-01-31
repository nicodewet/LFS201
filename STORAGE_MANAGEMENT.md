# Storage Management

## Create a virtual loop device `/dev/loop0` using 1GB file `/root/loopbackfile.img` with ext4 filesystem

Do not mount the device (we'll do that in the next step). Note `man losteup` has a handy example.

```
$ sudo dd if=/dev/zero of=/root/loopbackfile.img bs=100M count=10
$ sudo du -sh /root/loopbackfile.img
$ sudo losetup --find --show --partscan /root/loopbackfile.img
$ sudo losetup --all
$ sudo mkfs.ext4 /root/loopbackfile.img
```

## Temporarily mount `/dev/loop0` on `/mnt/backup`, make a `.tar.bz2` file on `/mnt/backup`, then unmount

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

## Temporarily mount `/dev/loop0` on `/mnt/backup`, decompress and unarchive `/mnt/backup/tmp.tar.bz2` to `/opt`

```
$ sudo tar -xvf /mnt/backup/tmp.tar.bz2 -C /opt
$ sudo mount -o loop /dev/loop0 /mnt/backup
```

## List all partition table devices as mentioned in `/proc/partitions`

```
$ sudo fdisk -l
```

### Create a new partition and force the system to re-read the modified partition table without a reboot

```
$ sudo fdisk /dev/sda
$ sudo partprobe -s
```

## Use the kernel device-mapper crypt target (dm-crypt) to implement transparent block device encryption

- [dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/-/wikis/DMCrypt)

Assumption `/dev/sda` is a USB key mounted by UUID as follows in `\etc\fstab`:

```
UUID=914fb199-*-d31e872ed8fd	/mnt/kinga	ext4	defaults,nofail,x-systemd.device-timeout=5	1	1
```

Remember you can get device info as follows:

```
$ sudo udisksctl info --block-device /dev/sda
```

### Install cryptsetup

```
$ dpkg -s cryptsetup
$ apt-cache show cryptsetup
$ apt-cache showpkg cryptsetup
$ sudo apt -y install cryptsetup
$ dpkg -s cryptsetup
```

### Fully encrypt raw block device (usb key) with cryptsetup using the LUKS2 on-disk key store format

LUKS, as per `man cryptsetup`, stands for `Linux Unified Key Setup` which is a standard for disk encryption. `It
adds a standard header at the start of the device, a key-slot area directly behind the header and the bulk area
behind that. The whole setup is called a LUKS container. The device that a LUKS container resides on is called
a LUKS device.`

Read the entire `LUKS EXTENSION` section of `$ man cryptsetup`

```
$ sudo umount /mnt/kinga
$ sudo cryptsetup luksFormat --type luks2 /dev/sda
```

### Create the un-encrypted pass through device by opening the encrypted block device, i.e., secret-disk.

```
$ sudo cryptsetup luksOpen /dev/sda secret-disk
Enter passphrase for /dev/sda:
$ ls /dev/mapper/secret-disk
```

### Format the filesystem as an ext4 filesystem.

```
$ sudo mkfs -t ext4 /dev/mapper/secret-disk
```

### Create a mount point for the new filesystem, `/mnt/kinga`

```
$ sudo mkdir -p /mnt/kinga
```