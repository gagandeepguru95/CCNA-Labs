# OSPF backup with floating static routes

Packet Tracer lab. Two Enterprise A sites joined by a private link running OSPF. I added a backup path through the ISP using floating static routes, then broke the main link to test it.

## Setup

- R1 and R2 are the two site routers
- 10.0.0.0/30 between them, running OSPF
- Both also connect to the ISP through SPR1 and SPR2
- PC1 is 10.0.1.1, SRV1 is 10.0.2.1

## What I found first

Enterprise A runs OSPF. One route in it.

```
R1#show ip route ospf
O    10.0.2.0 [110/2] via 10.0.0.2, 00:03:30, GigabitEthernet0/2/0
```

PC1 uses that to reach SRV1. For internet it uses the default static.

```
S*   0.0.0.0/0 [1/0] via 203.0.113.9
```

## Floating statics

OSPF is AD 110. So I set the statics to 120 to keep them out of the table.

```
R1(config)#ip route 10.0.2.0 255.255.255.0 203.0.113.1 120
R2(config)#ip route 10.0.1.0 255.255.255.0 203.0.113.5 120
```

They did not show in the table. That is what I wanted. OSPF still wins.

The ISP routers needed routes too or nothing would come back.

```
SPR1(config)#ip route 10.0.2.0 255.255.255.0 192.168.1.2 120
SPR2(config)#ip route 10.0.2.0 255.255.255.0 203.0.113.6 120
SPR2(config)#ip route 10.0.1.0 255.255.255.0 192.168.1.1 120
```

On SPR1 and SPR2 the AD 120 routes did show up right away. Nothing else was competing for those prefixes, so they installed.

## Breaking the link

```
R1(config)#int g0/2/0
R1(config-if)#shutdown

%OSPF-5-ADJCHG: Process 1, Nbr 203.0.113.14 on GigabitEthernet0/2/0
                from FULL to DOWN, Neighbor Down: Interface down or detached
```

Straight from FULL to DOWN. No Dead timer wait.

The static took over.

```
S    10.0.2.0/24 [120/0] via 203.0.113.1
```

Tracert from PC1 before:

```
1  10.0.1.254
2  10.0.0.2
3  10.0.2.1
```

After:

```
1  10.0.1.254
2  203.0.113.1
3  192.168.1.2
4  203.0.113.6
5  10.0.2.1
```

TTL went 126 to 124. Two routers to four. Matches the tracert.

## Mistakes

**`show ip route protocols`** — not a command. It is `show ip protocols` or `show ip route ospf`. The router treated my typo as a hostname and tried DNS.

**`show ip route g0/2/0`** — also not a command. Use `show ip route connected` or `show ip interface brief`.

**Forgot the next hop.**

```
SPR2(config)#ip route 10.0.1.0 255.255.255.0 120
                                              ^
% Invalid input detected at '^' marker.
```

Syntax is `ip route <network> <mask> <next-hop> <AD>`. I jumped straight to the AD.

**Added R2 static after the shutdown.** So R2 had no route back to 10.0.1.0/24 for a while. Backup routes need to be in before the failure, not after.

## Notes

First ping timed out once then worked. That is ARP. After the shutdown it was 3 timeouts, that is reconvergence. Next ping was clean 4 of 4.

Both ISP routers point their default at each other. SPR1 to 192.168.1.2, SPR2 to 192.168.1.1. Did not break this lab because the /24 routes are longer matches, but it looks wrong.

## Takeaways

- Lower AD wins. Connected 0, static 1, OSPF 110.
- Static with a higher AD than the dynamic protocol becomes a backup that stays hidden.
- Floating static installs right away if nothing competes for that prefix.
- Need routes in both directions on every hop or return traffic dies.
- OSPF goes FULL to DOWN instantly on link loss.
- A real network would use a VPN here, not plain ISP transit.

## Todo

- [ ] `no shutdown` and confirm OSPF takes back over
- [ ] Time the failover with a continuous ping
