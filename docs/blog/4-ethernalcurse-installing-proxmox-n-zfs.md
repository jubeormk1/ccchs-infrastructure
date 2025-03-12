# EthernalCurse: Reinstalling Proxmox with a ZFS Pool

**UPDATE**: Given the risk that creates having the root storage without a redundant storage solution, we decided to modify the base structure. I need to update this post to reflect that.


## TL;DR

- Install Proxmox from a USB drive in the first hard drive giving Hostname, admin interface, address, etc in RAID1 mode using first two hard drives
- Pool the rest of storage disks in a ZFS Pool configured as RAID-Z1 (Tolerant to a single disk down or data corruption)
- Create datasets within the pool to hold different information
- In the next post assign those datasets (they work as a folder whitin the pool) to different information in the Proxmox config files

One footnote for this post is that everything has been done manually as I explored what to do. Putting this in ansible or a shell script would make things easier to replicate. Since this is an simple task and we are not thinking of scaling the Proxmox cluster beyond two hosts, I am going to keep the manual approach.

## Basic Installation

First steps are simple. Download latest version of Proxmox, copy it to an USB drive with [Ventoy](https://www.ventoy.net/en/index.html) and boot on usb device (F12 in IBM boot screen).

Storage: Installation target will be done as RAID-1 Using `sda1` and `sda2`. I tried to install everything in a ZFS pool, but the installation failed. Most likely GRUB 2 needs a fat32 or ext4 partition for /boot.

Country: Australia
Timezone: UTC
eno1 (first ethernet port and yellow cable marked as 1) as administrative interface
Hostname: ethernalcurse.local
Address: 192.168.70.126/24 (IBM IMM is 192.168.70.125/24)
Gateway: 192.168.70.1 (bad)
DNS: 1.1.1.1 (wont work with bad gateway)

Password: (Not to be shared in any of the documentation, of course)

## getting the ZFS RAID-Z2 up and running

RAID-Z2 will give us fault tolerance for 2 drive. Meaning that the system will maintain information integrity if one drive gets corrupted or missing.

Given the next hardware configuration:

**TODO: This listing needs update**
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
# Maybe will look like this but it is likely to be wrong since I am using a RAID 1:
sdb                  8:16    0 930.4G  0 disk
├─sdb1               8:17    0  1007K  0 part
├─sdb2               8:18    0     1G  0 part
└─sdb3               8:19    0 929.4G  0 part
  ├─pve-swap       252:5    0     8G  0 lvm 
  ├─pve-root       252:6    0    96G  0 lvm
  ├─pve-data_tmeta 252:7    0   8.1G  0 lvm
  │ └─pve-data     252:8    0 793.2G  0 lvm
  └─pve-data_tdata 252:9    0 793.2G  0 lvm
    └─pve-data     252:10    0 793.2G  0 lvm
sdc                  8:32   0 930.4G  0 disk
sdd                  8:48   0 930.4G  0 disk
sde                  8:64   0 930.4G  0 disk
sdf                  8:80   0 930.4G  0 disk
sdg                  8:96   0 930.4G  0 disk
```

The zpool is created as:

`zpool create -m /mnt/tank tank raidz2 /dev/sdc /dev/sdd /dev/sde /dev/sdf /dev/sdg`

```bash
root@ethernalcurse:~# zpool status
  pool: tank
 state: ONLINE
config:

        NAME         STATE     READ WRITE CKSUM
        tank  ONLINE       0     0     0
          raidz2-0   ONLINE       0     0     0
            sdc      ONLINE       0     0     0
            sdd      ONLINE       0     0     0
            sde      ONLINE       0     0     0
            sdf      ONLINE       0     0     0
            sdg      ONLINE       0     0     0

errors: No known data errors
```

**The pool has been mounted in `/mnt/tank/`**

## Segmenting the pool: Datasets

Datasets are optional. They help giving different properties such as Quotas, independent snapshots, compression, etc. Since we are going to use virtual machines and storage it looks like overkill to add this prevision.

To start, I have added three dataset to the storage pool, vs-disks, ISOs and backups. Adding them is trivial eg.`zfs create tank/vm-disks`. Backups and ISOs can be compressed and we most likely only decide to take snapshots of vs-disks while we do not want it to be compressed.


```bash
zfs set compression=off tank/vm-disks
zfs set compression=on tank/isos
zfs set compression=on tank/backups
```

## Next steps

- Configuring the storage in Proxmox to make use of the ZFS Pool and possibly datasets.
