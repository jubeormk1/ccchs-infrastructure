# EthernalCurse: Reinstalling Proxmox with a ZFS Pool

First steps are simple. Download latest version of Proxmox, copy it to an USB drive with [Ventoy](https://www.ventoy.net/en/index.html) and boot on usb device (F12 in IBM boot screen).

## Basic Installation

Country: Australia
Timezone: UTC
eno1 (first ethernet port and yellow cable marked as 1) as administrative interface
Hostname: ethernalcurse.local
Address: 192.168.70.126/24 (IBM IMM is 192.168.70.125/24)
Gateway: 192.168.70.1 (bad)
DNS: 1.1.1.1 (wont work with bad gateway)

Password: (Not to be shared in any of the documentation, of course)

## getting the ZFS RAID-Z1 up and running

RAID-Z1 will give us fault tolerance for 1 drive. Meaning that the system will maintain information integrity if one drive gets corrupted or missing.

Given the next hardware configuration:

```bash
root@ethernalcurse:~# lsblk
NAME               MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                  8:0    0 930.4G  0 disk
├─sda1               8:1    0  1007K  0 part
├─sda2               8:2    0     1G  0 part /boot/efi
└─sda3               8:3    0 929.4G  0 part
  ├─pve-swap       252:0    0     8G  0 lvm  [SWAP]
  ├─pve-root       252:1    0    96G  0 lvm  /
  ├─pve-data_tmeta 252:2    0   8.1G  0 lvm
  │ └─pve-data     252:4    0 793.2G  0 lvm
  └─pve-data_tdata 252:3    0 793.2G  0 lvm
    └─pve-data     252:4    0 793.2G  0 lvm
sdb                  8:16   0 930.4G  0 disk
sdc                  8:32   0 930.4G  0 disk
sdd                  8:48   0 930.4G  0 disk
sde                  8:64   0 930.4G  0 disk
sdf                  8:80   0 930.4G  0 disk
sdg                  8:96   0 930.4G  0 disk
```

The zpool is created as:

`zpool create storagepool raidz1 /dev/sdb /dev/sdc /dev/sdd /dev/sde /dev/sdf /dev/sdg`

```bash
root@ethernalcurse:~# zpool status
  pool: storagepool
 state: ONLINE
config:

        NAME         STATE     READ WRITE CKSUM
        storagepool  ONLINE       0     0     0
          raidz1-0   ONLINE       0     0     0
            sdb      ONLINE       0     0     0
            sdc      ONLINE       0     0     0
            sdd      ONLINE       0     0     0
            sde      ONLINE       0     0     0
            sdf      ONLINE       0     0     0
            sdg      ONLINE       0     0     0

errors: No known data errors
```

**The pool has been mounted in `/storagepool/`**

## Segmenting the pool: Datasets

To start, I have added two dataset to the storage pool, vs-disks, isos and backups. Adding them is trivial eg.`zfs create mypool/vm-disks`.

## Next steps

- Configuring the storage in Proxmox to make use of the ZFS Pool.
