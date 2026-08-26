# Dynamic ARP Inspection (DAI) Lab

In this lab I configured R1 as a DHCP server, enabled DHCP snooping on both switches, and then configured Dynamic ARP Inspection on SW1 and SW2. Everything was done in Cisco Packet Tracer as part of Jeremy's IT Lab CCNA course.

## Topology

- R1 connects to SW1 on G0/2
- SW1 connects to SW2 on G0/1
- PC1, PC2 and PC3 connect to SW2
- Everything is in VLAN 1

## Addressing

| Device | Interface | IP Address |
|---|---|---|
| R1 | LAN interface | 192.168.1.1 /24 |
| PC1 | Fa0 | DHCP |
| PC2 | Fa0 | DHCP |
| PC3 | Fa0 | DHCP |

Excluded range: 192.168.1.1 to 192.168.1.9

## Step 1: DHCP Server on R1

The excluded addresses are configured outside the pool, so I did that first.

```
enable
conf t
ip dhcp excluded-address 192.168.1.1 192.168.1.9
ip dhcp pool POOL1
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
```

## Step 2: DHCP Snooping on SW1

DHCP snooping has to be enabled globally and per VLAN. I also removed option 82 because Packet Tracer PCs do not handle it well. G0/2 faces R1, so that is the trusted port.

```
enable
conf t
ip dhcp snooping
ip dhcp snooping vlan 1
no ip dhcp snooping information option
interface g0/2
 ip dhcp snooping trust
 exit
```

I left G0/1 untrusted. It is the link to SW2, so untrusted is a bit more secure because SW1 can still catch any DHCP message that slipped past SW2. The trade off is more processing on SW1.

## Step 3: DHCP Snooping on SW2

Here G0/1 is the uplink to SW1, so that is the trusted port.

```
enable
conf t
ip dhcp snooping
ip dhcp snooping vlan 1
no ip dhcp snooping information option
interface g0/1
 ip dhcp snooping trust
 exit
```

## Step 4: DAI on SW2

I enabled DAI for VLAN 1 and turned on all three optional validation checks. All three have to go in one command. The order does not matter.

```
ip arp inspection vlan 1
ip arp inspection validate dst-mac ip src-mac
interface g0/1
 ip arp inspection trust
end
```

## Step 5: DAI on SW1

Same config, but here I trusted both uplinks.

```
conf t
ip arp inspection vlan 1
ip arp inspection validate dst-mac ip src-mac
interface range g0/1 - 2
 ip arp inspection trust
end
```

## Verification

```
show run
show ip arp inspection interfaces
```

`show run` confirmed the DAI and DHCP snooping lines at the top and the trust settings under each interface. On SW1, G0/2 is trusted for both DHCP snooping and DAI, while G0/1 is trusted for DAI only.

`show ip arp inspection interfaces` showed all ports untrusted by default, with only the ports I configured showing as trusted.

For the end to end test I switched PC1, PC2 and PC3 from static to DHCP. They all pulled an address, and a ping from PC1 to 192.168.1.1 worked, which means DAI was not dropping the ARP traffic.

```
ping 192.168.1.1
```

## Things I Noticed

- Packet Tracer does not support every DAI command from the lecture, so only the basics can be practiced here
- `show ip arp inspection interfaces` showed rate limiting at 15 pps on the trusted port too. Cisco documentation says rate limiting should be off by default on trusted ports, so this looks like a Packet Tracer quirk
- The three validate options must be entered together in one command. Entering them one at a time replaces the previous setting
- The port facing the DHCP server must be trusted for both DHCP snooping and DAI. That is the one setting you cannot get wrong
- Trusting the switch to switch link is a design choice, not a rule. Both options work
