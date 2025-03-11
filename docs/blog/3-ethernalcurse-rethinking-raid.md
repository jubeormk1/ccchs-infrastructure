# EthernalCurse: Rethinking RAID

Coming from a debate in the infrastructure discord channel for CCHS, we have loosely decided to undo the built in RAID and proceed instead with a ZFS pool.

This has the drawback that we will loose some performance but will break us free from the built in RAID Controller in case of a failure, making it easier to recover in case of a hardware issue.

As we do not have any service or VM running on EthernalCurse, it is a painless moment to take this action.

## Undoing the RAID

First of all, we should do some reading

- [RAID configuration](https://www.programmersought.com/article/56481068366/).
- [On the topic of disabling the RAID](https://serverfault.com/questions/295578/how-to-disable-the-built-in-RAID-in-x3650-m3)

1. Enter in the "WebBIOS" `ctr + H`. Waiting until the boot has started after any network boot has timed out
2. Note down RAID configuration (RAID 5)
3. Clear the RAID configuration
4. It is not possible to Disable the RAID. The workaround is to set every disk as an independent Drive Group
   - Raid level 0
   - Write Policy: Write Through
   - IO Policy: Direct
5. Add one by one the Drive groups to an individual span
6. Make the first drive Bootable

## Next steps

Once Proxmox is installed in the first drive, I will create a ZFS Pool with the remaining disks in RAID-Z1. More on this in the next post.