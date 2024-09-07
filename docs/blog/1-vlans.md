# Your first day at CCHS infrastructure

This series of posts are purely fictional, if anything described here matches reality it's just pure coincidence.

## VLANs, now

The chief security officer looks at the SGI rack with a few seconds worth of nostalgia and then swiftly rips all the ethernet cables from the switch, angry and frustrated. The following
dialog ensues with characters:

* RHM: Regular Hackerspace Member
* CSO: Chief Security Officer

```
RHM - Mate, why did you do that?
CSO - All hosts are on the same VLAN, that's unnacceptable, trouble brewing in this infrastructure.
RHM - Uhmm... ok, well, we just "winged it" until now and nothing has happened?
CSO - We got hacked last year.
RHM - Ooops, ok boss, carry on.
``` 

### The mission, should you wish to accept it

Fictional CSO broadcasts the following announcement to any keen member ([@coolcoder613eb](https://github.com/coolcoder613eb) seems to be raising a hand?):

We have two switches, Cisco SF-300-24 and another ASUS GigaX 1116i+ (manuals under `docs/manuals` folder), connected via fiber optic (trunk connection). 
Unfortuantely the access ports (regular "native" VLAN 1) is where every single human and machine connect to: WHAT A MESS!

Many members agree that no servers will ever connect to those switches again UNLESS the following checkboxes are ticked, for our own collective mental health and sleep quality:

- [ ] Figure out how to connect to the aforementioned switches by reading the manuals under docs folder in this repository.
- [ ] Define VLANs 1xx, 2xx, 3xx as discussed, each of which should be catering to regular USERS, SERVERS and [DMZ](https://en.wikipedia.org/wiki/DMZ_(computing)). Subject to change, but a good start.
- [ ] Make sure that users at the space have connectivity while performing those changes (changing plane wings while on flight).
- [ ] Assign those VLANs to ProxMox [multi-homed](https://en.wikipedia.org/wiki/Multihoming) servers (ArcaneBox and EternalCurse).
- [ ] Investigate if the switches above can handle layer 3 network constructs so that VLANs can potentially talk witch each other... otherwise add a router/firewall in the mix for that purpose.
- [ ] Points above documented under `docs/` folder as we go and "wing it" so that members don't see those servers as... well, black boxes.

This message will not self destruct, since there's no need :)

## Ground rules

1. No secrets (i.e default passwords) shall be published as part of the docs, ever.
2. Document what you have done.
3. Automate as much as possible.
4. This repo must remain public, sunlight is the best disinfectant.
