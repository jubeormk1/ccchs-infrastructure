# Notable hosts

192.168.0.27: eternalcurse.internal
192.168.0.17: arcanebox.internal
192.168.1.254: Cisco Switch ????

# VLAN on endpoint

This is how one configures a laptop for access to a VLAN 200 managed switch:

```shell
  ip link
  ip link add link enx9cbf0d001240 name vlan200 type vlan id 200
  ip address add 192.168.0.1/24 dev vlan200
```

# Switch Cisco SFP300-24

Has ports 1-6 and 13-18 configured as VLAN100 (servers).
The rest of the ports are assigned to VLAN1  (default)

192.168.1.254 is the default admin interface (on VLAN1).

The [`http` interface][enable_http_access] needs to be enabled through the serial console.

## Updating firmware (via TFTP)

To update the firmware on this (discontinued) managed switch, if it's too old, needs [a multi-step approach][whole_update_process], in short:

1. Run a TFTP server that points to the `.rfb` and `.ros` files (see next subsection on howto).
1. Upgrade the firmware to an intermediate bootloader version.
1. Switch the "active image" to the the recently uploaded image.
1. Reboot.
1. Upload the `.ros` firmware file via HTTP (since the bootloader will no longer complain about "invalid format").

Repeat until upgraded to the latest 2020 firmware and if something fails go through the documentation [here][sf300-24_troubleshooting], [here][blessed_firmware] and [here][sf300-24_boot_upgrade].

### Running a TFTP server w/ dnsmasq on Ubuntu

```sh
# sudo systemctl stop systemd-resolved
# dnsmasq --no-daemon --enable-tftp --tftp-root=$PWD/Sx300_FW_Boot_1.4.1.03 --tftp-no-blocksize --listen-address=192.168.1.1
```

Shutting down `systemd-resolved` will temporarily cripple your domain name resolution, but will avoid "port 53 already used" errors and you'll
have a working local TFTP server.

[enable_http_access]: https://community.cisco.com/t5/switching/can-t-access-web-interface-on-sf-300-24/td-p/2146915
[sf300-24_troubleshooting]: https://www.cisco.com/c/en/us/support/docs/smb/switches/cisco-small-business-300-series-managed-switches/smb5007-firmware-upgrade-troubleshooting-on-300-and-500-series-manag.html
[blessed_firmare]: https://software.cisco.com/download/home/283019680/type/282463181/release/1.4.1.03
[sf300-24_boot_upgrade]: https://www.cisco.com/c/en/us/support/docs/smb/switches/cisco-small-business-300-series-managed-switches/smb5862-Upgrade-Firmware-200-300-Switches.html#How-do-I-Upgrade-a-Boot-File-through-a-TFTP-Server
[whole_update_process]: https://www.reddit.com/r/Cisco/comments/15hsqjp/cisco_300_series_firmware_upgrade_sf30048/
