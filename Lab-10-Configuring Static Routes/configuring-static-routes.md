# Static Routes Configuration Lab

## Overview

This lab focuses on configuring static routes on Cisco routers to allow two PCs on different networks to communicate with each other. The lab starts from scratch with no pre-configurations, so basic setup like hostnames, IP addresses, and interface descriptions are also covered before getting into static routing. The end goal is to get PC1 and PC2 to successfully ping each other through two routers.

## Topology

There are three routers: R1, R2, and R3. PC1 is connected to a switch which connects to R1. PC2 is connected to a separate switch which connects to R3. R1 and R3 do not connect directly to each other. Instead, R2 sits in the middle, connecting to both R1 and R3. This means any traffic between PC1 and PC2 has to pass through all three routers. The networks used are 192.168.1.0/24 for PC1's LAN, 192.168.12.0/24 between R1 and R2, 192.168.13.0/24 between R2 and R3, and 192.168.3.0/24 for PC2's LAN.

## Key Concepts

**Basic Interface Configuration**

Before static routes can work, each router needs its interfaces configured with IP addresses and enabled. If an interface is not enabled, the connected route will not appear in the routing table, and the link between two routers will show as up/down until both sides are configured. Descriptions on interfaces are also a good habit for keeping things organized.

**Planning Static Routes Before Configuring**

Before typing any commands, it helps to think through which routes each router actually needs. R1 is already connected to PC1's network, so it only needs a route toward PC2's network. R3 is already connected to PC2's network, so it only needs a route toward PC1's network. R2 is in the middle and not connected to either end network, so it needs routes in both directions. In total, four static routes need to be configured across the three routers.

**Two-Way Reachability**

For a ping to work, packets need to reach the destination and replies need to make it back. This means every router in the path needs routes for both directions, not just one. Forgetting a route in the return direction is a common mistake that causes pings to fail even when the forward path looks correct.

**Exit Interface vs Next-Hop IP**

When configuring a static route, there are two main ways to tell the router where to send the packet. You can specify the IP address of the next-hop router, or you can specify which local interface to send the packet out of. Both approaches work on point-to-point links like the ones between these routers. In this lab, both methods were used across different routers just to practice each one.

**ARP and the First Ping**

The first ping attempt may fail even when everything is configured correctly. This is because the router needs to use ARP to learn the MAC address of the next-hop device before it can actually forward the packet. After that initial ARP exchange, the remaining pings go through fine.

## What I Learned

This lab made the idea of static routing much more concrete. It is one thing to understand the concept, but actually going through each router, thinking about which networks it does and does not know about, and then writing the correct route for each one made it click better. I also found it useful to plan out all the routes on paper before touching the CLI, because it is easy to miss one and then spend time wondering why the ping is not working. The fact that R2 needed two routes while R1 and R3 only needed one each was a good reminder that every router only knows what you tell it.

## Notes

- An interface showing up/down usually just means the other side has not been configured or enabled yet. It is not always a sign that something is wrong on your end.
- When configuring a static route with only the exit interface instead of the next-hop IP, a warning message may appear in the CLI. On point-to-point links like the ones in this lab, it can be safely ignored.
- Always verify the routing table after configuring routes to confirm they appear correctly before testing with a ping.
