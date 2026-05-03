# Ethernet LAN Switching Lab

## Overview

This lab explores how switches learn MAC addresses and how ARP works when devices communicate on the same network. The tasks involve predicting what messages get sent during a ping, verifying that in simulation mode, and then using show commands to inspect and clear the MAC address table on each switch.

## Topology

Two switches, SW1 and SW2, connected to each other. PC1 and PC2 connect to SW1, and PC3 and PC4 connect to SW2. All four PCs are on the same network, 192.168.1.0/24. Both switches start with empty MAC address tables and all PCs start with empty ARP tables.

## Key Concepts

**ARP Before a Ping**

When PC1 wants to ping PC3, it first needs PC3's MAC address. Since the ARP table is empty, PC1 sends a broadcast ARP request to every device on the network. PC3 responds with a unicast ARP reply back to PC1 only. Once PC1 has the MAC address, it can finally send the actual ICMP ping as a unicast message.

**Broadcast vs Unicast**

The ARP request goes to every device on the local network because it uses the broadcast MAC address, all Fs. The ARP reply, ICMP echo request, and ICMP echo reply are all unicast, meaning they go only to the intended device. PC2 and PC4 receive the ARP request but ignore it since it is not meant for them.

**How Switches Learn MAC Addresses**

Switches learn MAC addresses by looking at the source address of frames they receive. After pings are exchanged between PC1 and PC3, both switches add those MAC addresses to their tables. SW1 sees PC3 and PC4 as reachable through its uplink port to SW2, so it cannot tell them apart just from its own table. SW2 has them on separate ports so it knows exactly which is which.

**Viewing and Clearing the MAC Address Table**

The show mac address-table command displays what each switch has learned. The clear mac address-table dynamic command removes all dynamically learned entries. Packet Tracer does not support clearing entries by specific address or interface, only all at once.

## What I Learned

Walking through the ARP and ping process step by step made it much easier to understand why broadcasts happen and why unicast is used after that. It also showed why a switch might know a MAC address exists but not be able to tell exactly which device it belongs to without checking a downstream switch.

## Notes

- Windows PCs send 4 pings by default. Cisco devices send 5. Easy detail to mix up.
- The show mac address-table command needs a hyphen between address and table, not a space. Getting that wrong will cause an error.
- Always generate traffic from both pairs of PCs before checking the MAC table, otherwise some entries will be missing.
