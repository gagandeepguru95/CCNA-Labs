# CCNA Lab: VLAN Trunking and Router on a Stick

This is my lab work from Jeremy's IT Lab (Day 17 practice lab), done in Cisco Packet Tracer.

In this lab I set up access ports, trunk links between two switches, and router on a stick so hosts in different VLANs could talk to each other.

## Topology

- 1 router (R1)
- 2 switches (SW1, SW2)
- PCs spread across VLAN 10, VLAN 20 and VLAN 30
- SW1 to SW2 is a trunk (G0/1)
- SW2 to R1 is a trunk (G0/2)

## Addressing

Each VLAN uses a /26 subnet. The router uses the last usable address of each subnet as the gateway.

| VLAN | Subnet | Router address |
|------|--------------------|----------------|
| 10 | 10.0.0.0/26 | 10.0.0.62 |
| 20 | 10.0.0.64/26 | 10.0.0.126 |
| 30 | 10.0.0.128/26 | 10.0.0.190 |

## What I configured

### Step 1: Access ports

On SW1:

```
enable
configure terminal
interface range f0/1 - 2
 switchport mode access
 switchport access vlan 10
interface range f0/3 - 4
 switchport mode access
 switchport access vlan 30
```

On SW2:

```
enable
configure terminal
interface f0/1
 switchport mode access
 switchport access vlan 20
interface range f0/2 - 3
 switchport mode access
 switchport access vlan 10
```

The switch creates the VLAN automatically when you assign it to an access port.

### Step 2: Trunk between SW1 and SW2

Same config on both sides:

```
interface g0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,30
 switchport trunk native vlan 1001
```

I only allowed VLAN 10 and 30 here. There are no VLAN 20 hosts on SW1, so VLAN 20 traffic never needs to cross this link. It goes to R1 first and comes back in VLAN 10 or 30.

I also set an unused VLAN (1001) as the native VLAN instead of leaving it as VLAN 1.

### Step 3: Trunk between SW2 and R1

```
interface g0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 switchport trunk native vlan 1001
```

All three VLANs are allowed here because every VLAN needs to reach the router for inter-VLAN routing.

### Step 4: Router on a stick

```
enable
configure terminal
interface g0/0
 no shutdown
interface g0/0.10
 encapsulation dot1q 10
 ip address 10.0.0.62 255.255.255.192
interface g0/0.20
 encapsulation dot1q 20
 ip address 10.0.0.126 255.255.255.192
interface g0/0.30
 encapsulation dot1q 30
 ip address 10.0.0.190 255.255.255.192
```

The physical interface needs `no shutdown` because Cisco router interfaces are down by default.

## Problem I ran into

After setting the trunk on SW2, `show interfaces trunk` showed VLAN 10 and 30 as allowed, but only VLAN 10 was listed as active in the management domain.

The reason was that VLAN 30 did not exist on SW2. VLAN 10 and 20 got created automatically from the access port config, but SW2 has no VLAN 30 access ports. So I created it manually:

```
vlan 30
exit
```

After that both VLANs showed up as allowed and active.

## Verification

Commands I used to check the work:

```
show vlan brief
show interfaces trunk
show ip interface brief
```

Then I tested pings from a VLAN 10 PC:

- To another VLAN 10 PC (10.0.0.1): works, frame goes straight across, no routing
- To a VLAN 20 PC (10.0.0.65): works, traffic goes to R1 first and comes back
- To a VLAN 30 PC (10.0.0.129): works, traffic goes to R1, then back through SW2 and SW1

Simulation mode in Packet Tracer made the path easy to see.

## Notes

- This switch only supports 802.1Q, so `switchport trunk encapsulation dot1q` was not needed. On older switches that support ISL you have to run it first.
- The subinterface number does not have to match the VLAN ID, but matching them keeps things readable.
- Pick either the first or last usable address as the gateway and stick to it across the whole network.

## Credit

Lab and lecture from [Jeremy's IT Lab](https://www.youtube.com/c/JeremysITLab) free CCNA course.
