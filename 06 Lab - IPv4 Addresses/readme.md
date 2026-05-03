# Router Interface IP Address Configuration Lab

## Overview

This lab covers configuring IP addresses on a Cisco router's interfaces, adding interface descriptions, enabling the interfaces, and verifying everything is working with show commands and pings. Three different network classes are used across the three interfaces, which also gives some practice recognizing Class A, B, and C subnet masks.

## Topology

One router, R1, connected to three switches, SW1, SW2, and SW3. Each switch has a PC connected to it. The three networks are a Class A network on G0/0, a Class B network on G0/1, and a Class C network on G0/2. Each PC sits in one of those networks and uses R1 as its gateway.

## Key Concepts

**Router Interfaces Are Shutdown by Default**

Unlike switch interfaces, Cisco router interfaces come with the shutdown command applied by default. This means they will show as administratively down until you explicitly enable them with no shutdown. Both the status and protocol columns in show ip interface brief will be down until the interface is enabled.

**Subnet Masks for Each Class**

The subnet mask depends on the prefix length of the network. A /8 address uses 255.0.0.0, a /16 uses 255.255.0.0, and a /24 uses 255.255.255.0. Packet Tracer auto-fills the mask on PCs based on the address class, which is a helpful hint when checking your work.

**Interface Descriptions**

Adding a description to each interface is good practice for keeping track of what each interface connects to. It does not affect how the interface works, but it shows up in the running config and makes troubleshooting easier later.

**Saving the Configuration**

Changes are stored in the running config and will be lost on reboot unless saved. The write or wr command is the quickest way to save to the startup config.

## What I Learned

This lab made the process of bringing up a router interface feel straightforward. Configure the IP address, add a description, run no shutdown, and then verify with show ip interface brief. Doing it three times across different network classes also helped reinforce which subnet mask goes with which prefix length.

## Notes

- The do command is useful when you forget to drop back to privileged exec mode before running a show command. Typing do in front of the command works from anywhere.
- The end command jumps straight back to privileged exec mode from anywhere, which is faster than typing exit multiple times.
- Always check show ip interface brief after enabling interfaces to confirm both the status and protocol columns show up and not just one of them.
