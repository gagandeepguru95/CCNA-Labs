# OSPF Default Route Advertisement (ASBR) Lab

This lab is from Jeremy's IT Lab CCNA course. I built it in Cisco Packet Tracer.

The goal was to run OSPF on four routers, then make R1 an ASBR that injects a default route into the OSPF domain so every router can reach the internet through it.

---

## Topology

```
ISPR1 ---- R1 ---- 10.0.12.0/30 ---- R2
203.0.113.0/30 |                      |
               |                      |
        10.0.13.0/30           10.0.24.0/30
               |                      |
              R3 ---- 10.0.34.0/30 -- R4
                                      |
                                    SW1
                                      |
                                    PC1
                              192.168.4.0/24
```

All router links are in OSPF area 0. The link to ISPR1 is not in OSPF.

---

## IP Addressing

| Device | Interface | IP Address |
|--------|-----------|------------|
| R1 | G3/0 | 203.0.113.1/30 |
| R1 | G0/0 | 10.0.12.1/30 |
| R1 | F1/0 | 10.0.13.1/30 |
| R1 | Loopback0 | 1.1.1.1/32 |
| R2 | G0/0 | 10.0.12.2/30 |
| R2 | F1/0 | 10.0.24.1/30 |
| R2 | Loopback0 | 2.2.2.2/32 |
| R3 | F1/0 | 10.0.13.2/30 |
| R3 | F2/0 | 10.0.34.1/30 |
| R3 | Loopback0 | 3.3.3.3/32 |
| R4 | F1/0 | 10.0.24.2/30 |
| R4 | F2/0 | 10.0.34.2/30 |
| R4 | G0/0 | 192.168.4.254/24 |
| R4 | Loopback0 | 4.4.4.4/32 |
| PC1 | NIC | 192.168.4.1/24 (GW 192.168.4.254) |
| ISPR1 | G3/0 | 203.0.113.2/30 |

---

## Configuration

### R1 (ASBR)

R1 has a static default route pointing at the ISP, and it advertises that default route into OSPF.

```
hostname R1
!
interface g3/0
 ip address 203.0.113.1 255.255.255.252
 no shutdown
interface g0/0
 ip address 10.0.12.1 255.255.255.252
 no shutdown
interface f1/0
 ip address 10.0.13.1 255.255.255.252
 no shutdown
interface loopback0
 ip address 1.1.1.1 255.255.255.255
!
ip route 0.0.0.0 0.0.0.0 203.0.113.2
!
router ospf 1
 router-id 1.1.1.1
 passive-interface loopback0
 default-information originate
!
interface g0/0
 ip ospf 1 area 0
interface f1/0
 ip ospf 1 area 0
interface loopback0
 ip ospf 1 area 0
```

I did not enable OSPF on G3/0, since that is the internet link.

### R3

```
hostname R3
!
interface f1/0
 ip address 10.0.13.2 255.255.255.252
 no shutdown
interface f2/0
 ip address 10.0.34.1 255.255.255.252
 no shutdown
interface loopback0
 ip address 3.3.3.3 255.255.255.255
!
line console 0
 logging synchronous
!
router ospf 1
 router-id 3.3.3.3
 passive-interface loopback0
!
interface f1/0
 ip ospf 1 area 0
interface f2/0
 ip ospf 1 area 0
```

### R4

```
hostname R4
!
interface f1/0
 ip address 10.0.24.2 255.255.255.252
 no shutdown
interface f2/0
 ip address 10.0.34.2 255.255.255.252
 no shutdown
interface g0/0
 ip address 192.168.4.254 255.255.255.0
 no shutdown
interface loopback0
 ip address 4.4.4.4 255.255.255.255
!
router ospf 1
 router-id 4.4.4.4
 passive-interface loopback0
 passive-interface g0/0
!
interface f1/0
 ip ospf 1 area 0
interface f2/0
 ip ospf 1 area 0
interface g0/0
 ip ospf 1 area 0
```

R2 was configured the same way with router-id 2.2.2.2.

---

## Verification

Neighbor adjacencies came up as expected:

```
%OSPF-5-ADJCHG: Process 1, Nbr 1.1.1.1 on FastEthernet1/0 from LOADING to FULL, Loading Done
%OSPF-5-ADJCHG: Process 1, Nbr 4.4.4.4 on FastEthernet2/0 from LOADING to FULL, Loading Done
```

R3 routing table after OSPF converged:

```
Gateway of last resort is 10.0.13.1 to network 0.0.0.0

O       10.0.12.0 [110/2] via 10.0.13.1, FastEthernet1/0
C       10.0.13.0 is directly connected, FastEthernet1/0
O       10.0.24.0 [110/2] via 10.0.34.2, FastEthernet2/0
C       10.0.34.0 is directly connected, FastEthernet2/0
O    192.168.4.0/24 [110/2] via 10.0.34.2, FastEthernet2/0
O*E2 0.0.0.0/0 [110/1] via 10.0.13.1, FastEthernet1/0
```

R4 routing table:

```
Gateway of last resort is 10.0.34.1 to network 0.0.0.0

O*E2 0.0.0.0/0 [110/1] via 10.0.34.1, FastEthernet2/0
               [110/1] via 10.0.24.1, FastEthernet1/0
```

The default route shows up as `O*E2`. R4 learned two equal cost paths to it, one through R3 and one through R2, so it load balances between them.

### Test from PC1

```
C:\>ping 203.0.113.2
Reply from 203.0.113.2: bytes=32 time<1ms TTL=252
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)

C:\>tracert 203.0.113.2
  1   192.168.4.254
  2   10.0.34.1
  3   10.0.13.1
  4   203.0.113.2
Trace complete.
```

PC1 reached the ISP router. The trace went R4 to R3 to R1, so this time the traffic took the R3 path.

---

## Mistakes and Troubleshooting

**1. Wrong interface name on R4**

```
R4(config-if)#int g0/0/0
               ^
% Invalid input detected at '^' marker.
```

I typed the interface name in the newer ISR format out of habit. This router model uses `g0/0`, not `g0/0/0`. Checking `show ip interface brief` first would have saved me the error.

**2. Route missing right after configuring an interface**

After bringing up F2/0 on R3, my first `show ip route` only listed 10.0.13.0 and the loopback. The 10.0.34.0 network was not there yet. A second later this appeared:

```
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet2/0, changed state to up
```

The route was missing only because the line protocol had not come up yet. Nothing was wrong with the config. I just checked too early.

**3. Loopback showing "changed state to down"**

Every time I created a loopback I got a down message followed immediately by an up message. This is normal. The interface flaps for a moment when it is first created.

**4. Console messages breaking my typing**

Log messages kept cutting into commands I was typing. I fixed it with:

```
line console 0
 logging synchronous
```

**5. Forgetting passive interfaces**

Loopbacks and the LAN interface on R4 do not have OSPF neighbors, so sending hellos out of them is wasted. I set `passive-interface loopback0` on each router and `passive-interface g0/0` on R4. The network is still advertised, the router just stops sending hellos on that interface.

---

## Quirks I Noticed in Packet Tracer

- Pinging `0.0.0.0` from PC1 returned replies from 192.168.4.254 instead of failing. Real gear would not answer like this. It is a Packet Tracer behavior, not a real result.
- OSPF adjacency messages sometimes appeared with a delay of several seconds after the interface was enabled.

---

## What I Took Away

- `default-information originate` on R1 is what pushes the default route into OSPF. Without it, the other routers have no way out even though OSPF is working fine.
- The default route arrives as `O*E2` with a metric of 1, because external type 2 routes keep the same metric no matter how far they travel. That is why R4 saw two equal cost paths and load balanced.
- R1 needs an actual default route in its own table first. It cannot advertise something it does not have.
- Do not enable OSPF on the link facing the ISP.
