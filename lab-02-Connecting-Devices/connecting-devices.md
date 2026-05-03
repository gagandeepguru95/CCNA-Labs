# Network Device Connections Lab

## Overview

This lab is about connecting network devices together using the correct cable type. The main decision in each connection is whether to use a copper straight-through, copper crossover, or fiber optic cable. Auto MDI-X is disabled in this lab, so the cable choice actually matters.

## Topology

The network has four routers: R1, R2, R3, and R4. There are eight switches connecting to the routers and to end devices. PC1, PC2, PC3, and SRV1 each connect to their own switch. The routers are spread across different distances, ranging from 50 meters to 3 kilometers apart.

## Key Concepts

**Straight-Through vs Crossover**

The cable choice depends on how each device type uses its pins. PCs and routers transmit on pins 1 and 2, and receive on pins 3 and 6. Switches do the opposite. So when connecting two different device types, like a PC to a switch or a switch to a router, a straight-through cable works fine. When connecting two devices of the same type, like switch to switch or router to router, a crossover cable is needed so the transmit pins line up with the receive pins on the other side.

**Fiber Optic and Distance**

Copper UTP cables work up to 100 meters. Beyond that, fiber is needed. Multimode fiber covers distances up to around 550 meters, and single-mode fiber can go much further, up to 30 to 40 kilometers or more. So the distance between devices determines which fiber type is appropriate.

## What I Learned

This lab made it clearer that cable selection is not random. You have to think about what type of device is on each end and how far apart they are. Getting this wrong means the connection either does not work at all or uses the wrong medium for the distance involved.

## Notes

- Packet Tracer does not distinguish between single-mode and multimode fiber, but it is worth thinking through which would apply in a real scenario.
- A common mistake is using a straight-through cable between two switches or two routers, which will not work without Auto MDI-X.
- Always check the distance before choosing copper or fiber.
