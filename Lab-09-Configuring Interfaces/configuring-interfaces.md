# Lab: Configuring Router and Switch Interfaces

## Overview

This lab is about setting up basic interface configurations on a router and two
switches that are all connected in a single LAN. The goal is to get comfortable
with the steps you would follow on a real network when bringing up devices for
the first time.

---

## Objectives

- Understand how to configure hostnames, IP addresses, and interface descriptions
- Learn the difference between router and switch interface defaults
- Practice disabling unused ports on a switch
- Get familiar with verifying configurations using show commands
- Learn different ways to save a device configuration

---

## Topology

There is one router called R1 connected to a switch called SW1. SW1 connects to
a second switch called SW2. Each switch has two end hosts connected to it,
making four PCs in total. All devices are part of the same LAN using the
172.16.0.0/16 network.

---

## Key Concepts

**Router interfaces are down by default.**
They need to be manually enabled. Switch interfaces are up by default, so unused
ports need to be manually shut down instead.

**Speed and duplex should be set manually on device-to-device links.**
Interfaces connected to end hosts can be left on auto.

**Descriptions are not required but are good practice.**
They make it easier to understand what each interface is connected to when
reviewing a config later.

**There are three ways to save the running configuration.**
All three produce the same result, just different syntax.

---

## What I Learned

Going through three devices in one lab made the workflow start to feel more
natural. I got more comfortable moving in and out of interface config mode and
using the interface range command saved a lot of time when configuring multiple
ports at once. Seeing the same steps repeated on R1, SW1, and SW2 helped the
process stick better than just reading about it.

---

## Notes

> Packet Tracer does not always behave like a real Cisco device. Manually
> configured speed and duplex showed as autonegotiated in the output, which
> would not happen on real hardware.

> If you configure speed and duplex on one side of a link but not the other,
> the interface will stay down until both sides match.

> It is easy to forget that router interfaces need `no shutdown` while switch
> interfaces do not. Mixing these up is a common beginner mistake.
