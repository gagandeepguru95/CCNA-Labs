# Inter-VLAN Routing with SVIs (Layer 3 Switch)

CCNA practice lab from Jeremy's IT Lab, done in Cisco Packet Tracer.

In this lab I removed the existing router on a stick setup and moved the inter-VLAN routing job to a Layer 3 switch using SVIs.

## Topology

- **R1** connects to **SW2** over a point to point Layer 3 link
- **SW2** is a multilayer switch and does the routing between VLANs
- **SW1** is a Layer 2 switch, connected to SW2 by a trunk
- PCs are split across VLAN 10, VLAN 20 and VLAN 30

The devices came preconfigured from the previous lab, so the VLANs and the trunk were already in place.

## Addressing

| Device | Interface | IP Address |
|---|---|---|
| R1 | G0/0 | 10.0.0.194/30 |
| SW2 | G1/0/2 | 10.0.0.193/30 |
| SW2 | VLAN 10 SVI | 10.0.0.62/26 |
| SW2 | VLAN 20 SVI | 10.0.0.126/26 |
| SW2 | VLAN 30 SVI | 10.0.0.190/26 |

## What I Did

### 1. Removed router on a stick from R1

Deleted the three subinterfaces and gave the physical interface an IP.

```
R1(config)# no interface g0/0.10
R1(config)# no interface g0/0.20
R1(config)# no interface g0/0.30
R1(config)# interface g0/0
R1(config-if)# ip address 10.0.0.194 255.255.255.252
```

### 2. Made SW2's G1/0/2 a routed port

Reset the port first, then switched it to Layer 3 mode.

```
SW2(config)# default interface g1/0/2
SW2(config)# interface g1/0/2
SW2(config-if)# no switchport
SW2(config-if)# ip address 10.0.0.193 255.255.255.252
```

### 3. Enabled routing and added a default route

```
SW2(config)# ip routing
SW2(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.194
```

### 4. Created the SVIs

```
SW2(config)# interface vlan 10
SW2(config-if)# ip address 10.0.0.62 255.255.255.192
SW2(config)# interface vlan 20
SW2(config-if)# ip address 10.0.0.126 255.255.255.192
SW2(config)# interface vlan 30
SW2(config-if)# ip address 10.0.0.190 255.255.255.192
```

## Verification

```
show ip interface brief
show ip route
show vlan brief
```

- All three SVIs came up as UP/UP
- Ping from PC7 (VLAN 10) to PC3 (VLAN 30) worked
- In simulation mode the traffic went from SW2 straight to SW1, so R1 was not involved in the routing anymore
- Ping to 1.1.1.1 worked, which confirmed the default route to R1

## Things I Noticed

- `ip address` was not available on G1/0/2 until I ran `no switchport`. The port is in Layer 2 mode by default.
- No routes showed up at all before I enabled `ip routing`.
- `default interface` did not take effect the first time. I had to run it twice, likely a Packet Tracer quirk.
- Only the connected route appeared after configuring the routed port. The local route was missing, which also looks like a Packet Tracer thing.
- The deleted subinterfaces disappeared right away here. On real IOS they stay listed as DELETED until a reload.
- This Layer 3 switch needed `switchport trunk encapsulation dot1q`, unlike the switch model used in the earlier lab.
- The first ping or two failed because the PC had to ARP for its default gateway first.

## Commands Used

| Command | Purpose |
|---|---|
| `no interface g0/0.10` | Delete a subinterface |
| `default interface g1/0/2` | Reset an interface to defaults |
| `no switchport` | Turn a switchport into a routed port |
| `ip routing` | Enable routing on the switch |
| `interface vlan 10` | Create an SVI |
| `show ip route` | Check the routing table |
| `show vlan brief` | Confirm VLANs exist |

## Credit

Lab by [Jeremy's IT Lab](https://www.youtube.com/c/JeremysITLab), free CCNA course.
