# Port Security Lab (CCNA)

In this lab I configured port security on two switches in Cisco Packet Tracer. I used the shutdown violation mode on SW1 and the restrict violation mode on SW2, then I broke things on purpose to see how each mode reacts.

## Topology

```
PC1 --- F0/1 \
PC2 --- F0/2  >  SW1  --- G0/1 --- G0/1 --- SW2 --- R1
PC3 --- F0/3 /
```

All devices are in VLAN 1 on the 10.0.0.0/24 network. R1 is the gateway at 10.0.0.254.

## Addressing

| Device | Interface | IP Address |
| --- | --- | --- |
| R1 | G0/0 | 10.0.0.254 /24 |
| SW1 | VLAN 1 SVI | 10.0.0.10 /24 |
| PC1 | Fa0 | 10.0.0.1 /24 |
| PC2 | Fa0 | 10.0.0.2 /24 |
| PC3 | Fa0 | 10.0.0.3 /24 |

## What I Configured

### SW1 (ports to the PCs)

Shutdown violation mode, 1 MAC address allowed, sticky learning off, aging time of 1 hour.

```
enable
configure terminal
interface range f0/1 - 3
 switchport mode access
 switchport port-security aging time 60
 switchport port-security
```

Shutdown mode, a maximum of 1 address, and sticky learning being off are all defaults, so I did not have to type them. The default aging time is 0 minutes, which means secure MAC addresses never age out, so I set it to 60.

### SW2 (uplink to SW1)

Restrict violation mode, a maximum of 4 addresses, sticky learning on.

```
enable
configure terminal
interface g0/1
 switchport mode access
 switchport port-security violation restrict
 switchport port-security maximum 4
 switchport port-security mac-address sticky
 switchport port-security
```

I used 4 addresses instead of 3 because SW2 also learns SW1's MAC address from the CDP messages SW1 sends.

## Verification

```
show port-security
show port-security interface f0/1
show port-security interface g0/1
show mac address-table
show running-config
```

After pinging R1 from all three PCs, SW2 had learned 4 sticky MAC addresses on G0/1: the three PCs and SW1. In the MAC address table those entries show up as STATIC even though they were learned dynamically. That is what sticky learning does. The same addresses also appear in the running config under G0/1.

## Testing the Violation Modes

### Restrict on SW2

I created a VLAN 1 SVI on SW1 and pinged R1 from the switch itself.

```
interface vlan 1
 ip address 10.0.0.10 255.255.255.0
 no shutdown
```

The ping failed. SW2 already knew the MAC of SW1's G0/1 interface, but not the MAC of the SVI, so the frames were dropped. On SW2 the port status stayed secure-up and the violation counter went up by one. The port keeps working for allowed addresses.

### Shutdown on SW1

I changed the MAC address of PC1 from the Config tab, under FastEthernet0, and pinged R1 again.

The ping failed and F0/1 went down. `show port-security interface f0/1` showed a port status of secure-shutdown with a violation count of 1.

To bring the port back I had to do it manually:

```
interface f0/1
 shutdown
 no shutdown
```

## Notes and Quirks

* `switchport port-security` is rejected if the port is still in the default dynamic auto mode. The interface has to be set to access or trunk first. I hit this on SW1 and had to add `switchport mode access` before it would take.
* Packet Tracer does not print the port security syslog messages. On real gear a violation logs a message to the console. Here I only saw the generic interface down message.
* `errdisable recovery` is not supported in Packet Tracer, so a secure-shutdown port has to be recovered by hand.
* Some of the port security commands from the lecture video do not exist in Packet Tracer, but the main ones all work.
