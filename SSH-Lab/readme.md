# CCNA Lab: Switch Security and SSH Remote Access

This lab is from Jeremy's IT Lab CCNA course. I configured a new switch (SW2) with basic settings, secured the console line, and set up SSH for remote access. Access to SSH is limited to one PC using an ACL.

## Topology

- **Laptop1** connects to SW2 through the console port
- **SW2** is the switch I configured
- **R2** and **PC1** are used to test remote access

## What I Did

### 1. Basic Setup

I connected Laptop1 to SW2 with a console cable, opened the terminal, and set the basics.

```
enable
conf t
hostname SW2
enable secret ccna
username jeremy secret ccna
```

Then I added a management IP on the VLAN1 SVI and a default gateway.

```
interface vlan1
 ip address 192.168.2.253 255.255.255.0
 no shutdown
exit
ip default-gateway 192.168.2.254
```

Notes:
- On this switch the SVI is shut down by default, so `no shutdown` is needed.
- I used `secret` instead of `password` because it stores a stronger hash.
- The enable secret lets you reach privileged exec mode when you connect over SSH.

### 2. Console Line Security

```
line console 0
 login local
 exec-timeout 5
```

`login local` means you have to log in with the account I made. The exec timeout logs you out after 5 minutes of no activity.

### 3. SSH Remote Access

SSH needs RSA keys, and RSA keys need a domain name first.

```
ip domain name jeremysitlab.com
crypto key generate rsa
```

I used a 2048-bit key. Then I made a standard ACL to allow only PC1 and applied everything to the VTY lines.

```
access-list 1 permit host 192.168.1.1
line vty 0 15
 login local
 exec-timeout 5
 transport input ssh
 access-class 1 in
```

- `transport input ssh` blocks Telnet and only allows SSH.
- `access-class 1 in` ties the ACL to the VTY lines.

## Testing

| Device | Ping SW2 | SSH to SW2 |
|--------|----------|------------|
| R2     | Works    | Refused (ACL blocks it) |
| PC1    | Works    | Works |

R2 can ping but cannot SSH because the ACL only permits PC1. PC1 connects fine using username `jeremy` and password `ccna`.

## Key Takeaways

- Use `secret` over `password` for stronger security.
- A switch needs a domain name before it can create RSA keys.
- ACLs on VTY lines control who is allowed to connect remotely.
