# Seasoned switch operator

We got access to the Cisco SF300-24 switch via a USB to Serial port converter cable (some sex reassignment required):

![../img/icusb232.bom.jpg]

After resetting the switch to the default factory settings and to our surprise, the default ip `192.168.1.254` for (web) interface configuration is nowhere to be found... instead there's `192.168.0.11` defined for some reason:

```
switch0aed53#show ip interface

  Gateway IP Address        Activity status       Type

----------------------- ----------------------- --------

    IP Address         I/F       Type       Status

------------------- --------- ----------- -----------

192.168.0.11/24    vlan 1    Static      Valid
```

After making sure that management IP is what it should be and [ignoring cisco community advice](https://community.cisco.com/t5/switching/can-t-access-web-interface-on-sf-300-24/td-p/2146915), we get it working as it should.

Defining VLAN tagging with the web interface for ports **1-6 and 13-18** is a breeze, now we just have to follow our own advice and make sure we are happy with the VLAN assignments we are proposing... or shall we go through a [Oxide computer RFD process](https://rfd.shared.oxide.computer/rfd/0001)?

# Next up

After checking the remaining check boxes of the previous blog, we should start thinking about defining our own DHCP/DNS arrangements and [mapping VLANs to and from Proxmox](https://pve.proxmox.com/wiki/Network_Configuration#sysadmin_network_vlan). So, more checkboxes to check out:

- [ ]: Define redundant DHCP/DNS servers for our new broadcast domains (VLANs 100, 200 and 300).
- [ ]: Discuss [split-horizon DNS configurations](https://en.wikipedia.org/wiki/Split-horizon_DNS).
- [ ]: Route and firewall our newly defined VLANs appropriatedly via [Proxmox SDN](https://pve.proxmox.com/pve-docs/chapter-pvesdn.html)
- [ ]: Discuss/configure/(trunk?) the other ASUS switch... probably reserve it for endpoints (laptops/users at the space)?.
- [ ]: Discuss and replace noisy fan on Allied Telesys POE switch, which will become really handy for IoT powering of the space's access point, cameras and other IoT devices.
