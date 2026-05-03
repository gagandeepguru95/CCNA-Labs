# Static Routes Configuration Lab 2

## Overview

This lab covers configuring static routes on three routers from scratch to allow two PCs on separate networks to communicate with each other. Since no pre-configurations were done, the lab also includes basic setup like hostnames, IP addresses, and interface descriptions before getting into the static routing portion.

## Topology

Three routers, R1, R2, and R3, connected in a chain. PC1 connects through a switch to R1, and PC2 connects through a separate switch to R3. R2 sits in the middle connecting R1 and R3. The networks are 192.168.1.0/24 for PC1's LAN, 192.168.12.0/24 between R1 and R2, 192.168.13.0/24 between R2 and R3, and 192.168.3.0/24 for PC2's LAN.

## Key Concepts

**Planning Routes Before Configuring**

Before touching the CLI, it helps to think through which routes each router needs. R1 already knows its own connected networks, so it only needs a route to PC2's network. R3 is in the same situation but on the other side. R2 is in the middle and not connected to either end network, so it needs routes in both directions. In total, four static routes are needed across the three routers.

**Two-Way Reachability**

A ping only works if packets can reach the destination and replies can make it back. Every router in the path needs routes covering both directions. Missing even one route on the return path will cause the ping to fail silently, which can be confusing to troubleshoot.

**Exit Interface vs Next-Hop IP**

When configuring a static route, you can either specify the IP address of the next-hop router or the local interface to send traffic out of. Both methods work on point-to-point links. A warning message may appear when using the exit interface method, but it can be ignored on direct router-to-router connections.

**UP/DOWN on an Interface**

An interface showing up/down is normal when the other side has not been configured yet. It is not a misconfiguration on your end. Once both sides are enabled, the link comes up to up/up.

## What I Learned

Going through the basic configurations from scratch each time is repetitive but it does help the commands stick. The more useful part was thinking through the route planning before configuring anything, because it made it clear exactly how many routes were needed and which router needed which one. Without that step it would be easy to miss R2 needing routes in both directions.

## Notes

- The first ping may fail due to ARP resolving in the background. This is normal and not a sign that something is misconfigured.
- Always check the routing table with show ip route after adding a static route to confirm it appears correctly before testing with a ping.
- R2 needing two routes while R1 and R3 only need one is easy to forget. It is the middle router that requires the most attention in a chain like this.
