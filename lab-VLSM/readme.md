# CCNA Lab - Subnetting with VLSM

A hands-on Packet Tracer lab practicing Variable-Length Subnet Masks (VLSM) on a Class C network.

---

## Objective

Subnet the `192.168.5.0/24` network into 5 subnets of different sizes and configure static routes so all hosts can reach each other.

---

## Subnets

| Subnet | Hosts Needed | Prefix | Network Address | Broadcast Address |
|--------|-------------|--------|-----------------|-------------------|
| LAN2 | 64 | /25 | 192.168.5.0 | 192.168.5.127 |
| LAN1 | 45 | /26 | 192.168.5.128 | 192.168.5.191 |
| LAN3 | 14 | /28 | 192.168.5.192 | 192.168.5.207 |
| LAN4 | 9 | /28 | 192.168.5.208 | 192.168.5.223 |
| Point-to-Point | 2 | /30 | 192.168.5.224 | 192.168.5.227 |

---

## IP Assignments

- Each PC gets the first usable address in its subnet
- Each router interface gets the last usable address in its subnet

---

## Tasks Completed

- Subnetted 192.168.5.0/24 using VLSM
- Configured IP addresses on all router interfaces and PCs
- Configured static routes on R1 and R2
- Verified connectivity with ping

---

## Tools Used

- Cisco Packet Tracer

## Status

Lab completed as part of CCNA self-study (Jeremy's IT Lab - Day 15).
