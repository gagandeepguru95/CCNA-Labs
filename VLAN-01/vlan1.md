# Lab: VLAN Access Port Configuration

## What I Did
I set up 3 VLANs on a Cisco switch and configured a router to allow traffic between them.

## Network Setup
- VLAN 10 (Engineering) - PC1, PC2
- VLAN 20 (HR) - PC3, PC4
- VLAN 30 (Sales) - PC5, PC6
- 1 switch (SW1) and 1 router (R1)

## Step 1: Configured the PCs
I set an IP address, subnet mask, and gateway on each PC.

| PC | VLAN | IP Address | Subnet Mask | Gateway |
|----|------|-----------|-------------|---------|
| PC1 | 10 | 10.0.0.1 | 255.255.255.192 | 10.0.0.62 |
| PC2 | 10 | 10.0.0.2 | 255.255.255.192 | 10.0.0.62 |
| PC3 | 20 | 10.0.0.65 | 255.255.255.192 | 10.0.0.126 |
| PC4 | 20 | 10.0.0.66 | 255.255.255.192 | 10.0.0.126 |
| PC5 | 30 | 10.0.0.129 | 255.255.255.192 | 10.0.0.190 |
| PC6 | 30 | 10.0.0.130 | 255.255.255.192 | 10.0.0.190 |

## Step 2: Connected and Configured the Router
I connected 3 cables between R1 and SW1, one for each VLAN. Then I gave R1 one interface per VLAN, using the gateway address as the interface IP.

```
interface g0/0
ip address 10.0.0.62 255.255.255.192
no shutdown

interface g0/1
ip address 10.0.0.126 255.255.255.192
no shutdown

interface g0/2
ip address 10.0.0.190 255.255.255.192
no shutdown
```

I checked it worked with `show ip interface brief`.

## Step 3: Configured the Switch
I set each PC's port as an access port and assigned it to the right VLAN.

```
interface range g0/1, f3/1, f4/1
switchport mode access
switchport access vlan 10

interface range g1/1, f5/1, f6/1
switchport mode access
switchport access vlan 20

interface range g2/1, f7/1, f8/1
switchport mode access
switchport access vlan 30
```

I also renamed the VLANs so they're easier to identify.

```
vlan 10
name ENGINEERING

vlan 20
name HR

vlan 30
name SALES
```

I checked it worked with `show vlan brief`.

## Step 4: Tested Connectivity
- Pinged from PC1 to PC3 (different VLANs) - it worked.
- Pinged from PC1 to PC5 (different VLANs) - it worked.
- Watched the ping in simulation mode. It went PC1 to switch, to router, back to switch, then to PC3. The router did the routing between VLANs.
- Pinged the VLAN10 broadcast address from PC1. Only PC2 (also in VLAN10) got it, confirming the broadcast stayed inside the VLAN.

## What I Learned
An access port only belongs to one VLAN. A switch alone cannot send traffic between VLANs, it needs a router. Giving the router one interface per VLAN lets it route traffic between them.
