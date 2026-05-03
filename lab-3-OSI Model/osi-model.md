# OSI Model and Network Traffic Lab

## Overview

This lab uses Packet Tracer's simulation mode to observe real network traffic and see how the OSI model layers work in practice. The focus is on watching different protocols send data and identifying which layers each protocol operates at.

## Topology

There are two routers, R1 and R2, two switches, SW1 and SW2, one PC, and one server. The left side of the network, including SRV1, PC1, SW1, SW2, and R1's G0/0 interface, all belong to the 192.168.1.0/24 network. R1's G0/1 and R2's G0/0 interface form a separate network, 10.0.0.0/24. R1 connects the two networks together.

## Key Concepts

**Simulation Mode**

Packet Tracer's simulation mode lets you pause the network and inspect individual packets as they move between devices. You can click on any packet to see which OSI layers contain information and what that information looks like.

**Protocols at Different Layers**

Different protocols operate at different layers. STP, the spanning tree protocol, only has Layer 1 and Layer 2 information since it is a Layer 2 protocol. OSPF, used by routers to find the best paths, adds Layer 3 information including source and destination IP addresses. DHCP, which automatically assigns IP addresses to devices, has information all the way up to Layer 7, plus Layers 4, 3, 2, and 1. Layers 5 and 6 are not shown separately because in the TCP/IP model they are merged into the application layer.

**Encapsulation in Action**

When inspecting a packet in simulation mode, you can see the encapsulation process described in the lectures actually happening. For example, a Layer 2 Ethernet frame is built around the data, and the PDU name changes depending on the layer, such as a segment at Layer 4.

## What I Learned

Seeing the layers in a real packet made the OSI model feel less abstract. It is easier to remember which layer does what when you can actually click on traffic and see the headers being built around the data. The fact that DHCP touches almost every layer while STP only uses the bottom two really helped show why different protocols exist and what job each one does.

## Notes

- Layers 5 and 6 do not appear separately in Packet Tracer because the TCP/IP model combines them with Layer 7 into a single application layer.
- The first time seeing a packet with all seven layers visible can be a little overwhelming, but focusing on one layer at a time makes it manageable.
- Generating DHCP traffic manually by releasing and renewing an IP address is a simple way to produce Layer 7 traffic to inspect.
