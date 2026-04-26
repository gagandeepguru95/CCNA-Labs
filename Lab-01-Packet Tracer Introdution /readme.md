# Lab 01 — Packet Tracer Introduction

## What this lab is about

This was the first hands-on lab of my CCNA studies.
The goal was to get familiar with the Cisco Packet Tracer interface and learn how to build a basic network from scratch — no configuration yet, just placing devices and connecting them.

---

## What I practiced

- Downloading and setting up Cisco Packet Tracer
- Navigating the Packet Tracer user interface
- Placing network devices (routers, switches, firewalls, end devices)
- Connecting devices using the automatic connection type
- Renaming devices to match a network diagram
- Recreating a real-world style network topology

---

## Network I built

Recreated a network with two branches — New York and Tokyo — connected over the Internet.

```
[Attacker]
    |
[The Internet] (Router)
    |                    |
[FW1]                 [FW2]
    |                    |
  [R1]                 [R2]
    |                    |
  [SW1]               [SW2]
  /    \              /     \
[PC1] [PC2]      [SRV1]  [SRV2]
```

---

## Devices used

| Device | Model | Role |
|--------|-------|------|
| Router | Cisco 2911 | Internet, R1, R2 |
| Switch | Cisco Catalyst 2960 | SW1, SW2 |
| Firewall | Cisco ASA 5505 | FW1, FW2 |
| PC | Generic PC | PC1, PC2 |
| Server | Generic Server | SRV1, SRV2 |
| Laptop | Generic Laptop | Attacker |

---

## Key things I learned

- Packet Tracer is a free simulation tool by Cisco — no need to buy real hardware to practice
- You can place devices from the bottom left panel and connect them using the lightning bolt icon
- Holding **Ctrl** while clicking the lightning bolt keeps it selected so you can connect multiple devices faster
- The CLI (Command Line Interface) is how routers and switches are configured — will be used heavily going forward
- New versions of Packet Tracer can open old lab files, but old versions cannot open newer ones — always keep it updated

---

## Files in this folder

| File | Description |
|------|-------------|
| `README.md` | This file — summary of the lab |
| `Lab-01-Packet-Tracer-Intro.pkt` | Packet Tracer lab file |

---

## Status

Completed — moving on to next topic.
