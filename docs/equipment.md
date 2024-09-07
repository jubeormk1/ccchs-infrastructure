# Networking

* GigaX 1116i+ ASUS
* Cisco SF300-24
* AlliedTelesis 8100/24 POE

# Compute

Two multihomed machines, one with a large-ish hardware RAID5 disk array and the other much less powerful (8GB, 2 cores) but with a NVIDIA GPU (potentially useful for local inference).
 
## EternalCurse

[IBM x3650 M3](https://lenovopress.lenovo.com/tips0805-system-x3650-m3)

/!\ WARNING: DIMM on slot 24 flagged as faulty.

Main machine in the ProxMox cluster:

```
root@eternalcurse:~# cat /proc/cpuinfo
(...)
Intel(R) Xeon(R) CPU           E5649  @ 2.53GHz

(that's right, sweet 24 cores :D)

root@eternalcurse:~# free
Mem:        98960416

root@eternalcurse:~# zpool list
NAME    SIZE  ALLOC   FREE  CKPOINT  EXPANDSZ   FRAG    CAP  DEDUP    HEALTH  ALTROOT
rpool  6.34T  2.13G  6.34T        -         -     0%     0%  1.00x    ONLINE  -

root@eternalcurse:~# lsblk 
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda      8:0    0  6.4T  0 disk 
├─sda1   8:1    0 1007K  0 part 
├─sda2   8:2    0    1G  0 part 
└─sda3   8:3    0  6.4T  0 part 

Note: This zpool is defined on top of the underlying hardware RAID 5
```

## ArcaneBox

Regular PC on a 5U box... but has a GPU builtin.

```
root@arcanebox:~# cat /proc/cpuinfo
(...)
Intel(R) Core(TM)2 CPU          6600  @ 2.40GHz

root@arcanebox:~# free 
(...)
Mem:                                    8134428

root@arcanebox:~# lspci
(...)
05:00.0 VGA compatible controller: NVIDIA Corporation G98 [GeForce 8400 GS Rev. 2] (rev a1)
06:00.0 VGA compatible controller: NVIDIA Corporation GM206 [GeForce GTX 960] (rev a1)

root@arcanebox:~# zpool list
NAME    SIZE  ALLOC   FREE  CKPOINT  EXPANDSZ   FRAG    CAP  DEDUP    HEALTH  ALTROOT
rpool  3.62T  3.17G  3.62T        -         -     0%     0%  1.00x    ONLINE  -

root@arcanebox:~# lsblk 
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk 
├─sda1   8:1    0  1007K  0 part 
├─sda2   8:2    0     1G  0 part 
└─sda3   8:3    0 930.5G  0 part 
sdb      8:16   0 931.5G  0 disk 
├─sdb1   8:17   0  1007K  0 part 
├─sdb2   8:18   0     1G  0 part 
└─sdb3   8:19   0 930.5G  0 part 
sdc      8:32   0 931.5G  0 disk 
├─sdc1   8:33   0  1007K  0 part 
├─sdc2   8:34   0     1G  0 part 
└─sdc3   8:35   0 930.5G  0 part 
sdd      8:48   0 931.5G  0 disk 
├─sdd1   8:49   0  1007K  0 part 
├─sdd2   8:50   0     1G  0 part 
└─sdd3   8:51   0 930.5G  0 part 
sde      8:64   0 298.1G  0 disk 
sdf      8:80   0 596.2G  0 disk 
```

