# Virtual Memory Layer

## Determine current swap space provisioning and utilization

```
$ cat /proc/swaps
$ free -m
              total        used        free      shared  buff/cache   available
Mem:            984          81         449           0         453         761
Swap:             0           0           0
```

## Add 1GB of swap space, then remove it

Detail:
- Add 1GB of swap space using a swap file
- Assign appropriate user/group ownership and appropriate file mode bits
- confirm that its available for use
- remove the file from use and delete it

```
$ cat /proc/swaps
$ free -mh
$ sudo dd if=/dev/zero of=/swpfile bs=1MB count=1024
$ sudo chown root:root /swpfile
$ sudo chmod 600 /swpfile
$ sudo mkswap /swpfile
$ sudo swapon /swpfile
$ cat /proc/swaps
$ free -mh
$ sudo swapoff /swpfile
$ sudo rm /swpfile
```



