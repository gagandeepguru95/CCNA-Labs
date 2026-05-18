# Basic STP Configuration Lab

## Overview

This lab covers basic STP configurations on a four-switch network. The goal is to manually control which switch is the root bridge for each VLAN so that traffic is balanced across different links instead of all going the same way.

## Topology

Four switches (SW1, SW2, SW3, SW4) connected to each other through FastEthernet interfaces. SW3 and SW4 each have a PC connected on F0/3. Two VLANs are used: VLAN 1 and VLAN 2.

## Key Concepts

**Root Bridge**: STP picks a root bridge automatically based on the lowest bridge ID. By default, SW2 was the root for both VLANs. We manually changed this so SW1 is root for VLAN 1 and SW2 is root for VLAN 2, spreading traffic across different links.

**Port Roles**: Every non-root switch has one root port pointing toward the root. Other forwarding ports are designated. A port that is neither becomes an alternate port and blocks traffic to prevent loops.

**Port Cost**: A higher cost makes a port less preferred. Increasing the cost on SW4's F0/2 caused STP to pick F0/1 as the root port instead.

**Port Priority**: Used as a last tiebreaker after cost and bridge ID. In this lab, changing the priority had no effect because cost already decided the winner.

**PortFast and BPDU Guard**: PortFast skips the STP listening and learning states so an access port reaches forwarding immediately. BPDU Guard shuts the port down if it receives a BPDU, protecting against accidental switch connections.

## What I Learned

STP works on its own but does not always pick the best path. Manually setting root bridges per VLAN is a simple way to balance traffic. I also learned that cost matters more than priority when STP selects a root port, and that BPDU Guard is a good safety measure on any access port.

## Notes

- The "alternate" role in STP output means the port is blocking, not that it is unused.
- After BPDU Guard shuts a port down, you have to manually bring it back up with shutdown then no shutdown.
- Root primary and secondary commands handle the priority adjustment automatically, so you rarely need to set it by hand.
