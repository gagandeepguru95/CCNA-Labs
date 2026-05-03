# Interface Configuration Lab

## Overview

This lab covers configuring interfaces on one router and two switches within a single LAN. The tasks include setting hostnames, IP addresses, speed and duplex settings, interface descriptions, disabling unused interfaces, and saving the configuration on each device.

## Topology

A single LAN using the 172.16.0.0/16 network. R1 connects to SW1 via G0/0, SW1 connects to SW2 via G0/2, and four PCs connect to the two switches as end hosts. R1 would normally connect onward to other networks or the internet, but this lab focuses only on the local LAN setup.

## Key Concepts

**Speed and Duplex**

On interfaces connected to other networking devices, speed and duplex can be manually configured instead of left to autonegotiation. Gigabit interfaces use speed 1000, and full duplex is appropriate for switch-connected devices since there is no risk of collision like there would be with a hub. Interfaces connected to end hosts are typically left at auto settings.

**Disabling Unused Interfaces**

On switches, interfaces are enabled by default, unlike router interfaces which are shutdown by default. Unused switch interfaces should be manually shut down as a security best practice. The interface range command makes this efficient by letting you configure multiple interfaces at once instead of one by one.

**show interfaces status**

This command works on switches but not routers. It gives a quick overview of each interface including its connection status, speed, duplex, and description. It is useful for verifying configurations across all interfaces at a glance.

**Three Ways to Save**

This lab demonstrates all three methods of saving the running config to startup config: copy running-config startup-config, write memory, and write. All three do the same thing and any of them can be used.

## What I Learned

Using interface range to configure multiple interfaces at once saved a lot of time, especially when shutting down 22 unused fastethernet interfaces in one command. It also became clearer why manually setting speed and duplex matters, since mismatched settings between two connected interfaces can cause the link to stay down until both sides are configured consistently.

## Notes

- Packet Tracer shows a-1000 and a-full even when speed and duplex are manually configured, which is incorrect. On a real device it would display 1000 and full without the A prefix.
- If G0/0 on R1 shows up but protocol is down after configuration, it is likely because the connected switch interface has not been configured yet with matching speed and duplex settings.
- Always verify with show startup-config after saving to confirm the changes were actually written.
