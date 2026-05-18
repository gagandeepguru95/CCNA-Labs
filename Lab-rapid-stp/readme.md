# RSTP Port Roles and Link Types Lab

## Overview

This lab covers Rapid Spanning Tree Protocol (RSTP) port roles and link types. We analyze the topology to figure out which switch is the root bridge, determine port roles without using the CLI, and then manually configure link types on each interface.

---

## Topology

| Device | Connected To |
|--------|-------------|
| SW1 | SW2, SW3, Hub |
| SW2 | SW1, SW3, SW4, PCs |
| SW3 | SW1, SW2, SW4, PC |
| SW4 | SW2, SW3, PC |

SW1 connects to a hub on some interfaces. SW2 and SW3 are linked via GigabitEthernet. All other inter-switch links are FastEthernet. Only VLAN 1 is used in this lab.

---

## Key Concepts

### Root Bridge Election
All switches had the same priority of 32769, so MAC addresses were compared. SW1 had the lowest MAC address and became the root bridge.

### Backup Port
On the root bridge, a backup port appears when two interfaces share the same collision domain, for example when both connect to the same hub. Only one can be designated, the other becomes a backup port in discarding state. This is different from classic STP where it would simply be a non-designated port.

### Port Roles
- **Root Port** - the port with the lowest cost path to the root bridge
- **Designated Port** - the forwarding port on each network segment
- **Alternate Port** - a blocked port that loses the tiebreaker against a neighbor
- **Backup Port** - a blocked port on the root bridge sharing a collision domain

### Link Types
- **Point-to-Point** - full duplex link, typically between two switches
- **Shared** - half duplex link, typically connected to a hub
- **Edge Port** - a port with PortFast enabled, connected to an end host

A port can be both edge and point-to-point, or both edge and shared at the same time. Packet Tracer does not show this correctly but real switches do.

---

## What I Learned

I used to think all root bridge ports are always designated. This lab showed that is not always true. If the root bridge has two ports in the same collision domain, one becomes a backup port. I also learned that link types are set automatically based on duplex mode, so shared and point-to-point usually do not need manual configuration. The only thing we really needed to configure manually was PortFast on ports connected to end hosts.

---

## Notes

- Switches automatically detect the correct link type based on duplex, so manual link-type configuration is mostly for practice.
- PortFast must always be configured manually since it is not enabled by default.
- A hub does not add any STP cost to a BPDU passing through it.
