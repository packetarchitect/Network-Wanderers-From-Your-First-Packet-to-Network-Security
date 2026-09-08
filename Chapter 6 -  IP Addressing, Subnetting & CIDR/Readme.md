# 📘 Module 1 – Networking Fundamentals

# Chapter 6 -- IP Addressing, Subnetting & CIDR (Basic to Advanced)

IP addressing is the foundation of Layer 3 networking.

A network engineer must be able to answer:

``` text
What is the destination IP?
        ↓
Is the destination local or remote?
        ↓
What subnet does the host belong to?
        ↓
What is the network address?
        ↓
What is the broadcast address?
        ↓
What is the usable host range?
        ↓
Which default gateway should be used?
        ↓
Which route should forward the packet?
```

This chapter takes IP addressing from the fundamentals to practical
subnetting, CIDR, VLSM, route summarization, VLAN/firewall design,
troubleshooting, and interview-level subnetting.

------------------------------------------------------------------------

> **Module 1 \-- Networking Fundamentals → Advanced Networking**
>
> This chapter takes IP addressing from the fundamentals to practical
> subnetting, CIDR, VLSM, route summarization, troubleshooting, and
> interview-level decision making.

------------------------------------------------------------------------

# 6.1 What is an IP Address?

An **IP address (Internet Protocol address)** is a logical Layer 3
address used to identify a device/interface on an IP network.

Think of it like a **logical postal address**.

``` text
MAC Address
   ↓
Local Layer 2 delivery
   ↓
IP Address
   ↓
End-to-end Layer 3 delivery across networks
```

Example:

``` text
PC
IP Address: 192.168.10.25
```

The IP address helps routers determine:

> \"Which network should I send this packet toward?\"

------------------------------------------------------------------------

# 6.2 MAC Address vs IP Address

Feature MAC Address IP Address

------------------------------------------------------------------------

Layer Layer 2 Layer 3 Purpose Local delivery Logical/end-to-end routing
Example `00:1A:2B:3C:4D:5E` `192.168.10.25` Usually assigned by
NIC/vendor Network/DHCP/admin Used by Switches Routers/firewalls Changes
when moving networks? Usually no Often yes

------------------------------------------------------------------------

# 6.3 IPv4 Address Structure

IPv4 uses **32 bits**.

``` text
32 bits
│
├────────┬────────┬────────┬────────┐
 8 bits    8 bits    8 bits    8 bits
 Octet 1   Octet 2   Octet 3   Octet 4
```

Example:

``` text
192.168.10.25
```

Binary:

``` text
192        168        10         25
11000000 . 10101000 . 00001010 . 00011001
```

Therefore:

``` text
4 × 8 = 32 bits
```

------------------------------------------------------------------------

# 6.4 IPv6 \-- Brief Introduction

IPv6 uses **128 bits**.

Example:

``` text
2001:db8:1234:5678::10
```

IPv6 includes mechanisms such as:

-   Neighbor Discovery Protocol (NDP)
-   Stateless Address Autoconfiguration (SLAAC)
-   Link-local addressing
-   Multicast-based discovery

This chapter focuses mainly on IPv4 subnetting calculations.

------------------------------------------------------------------------

# 6.5 Public IP vs Private IP

Private IPv4 ranges are defined by RFC 1918.

Private Range CIDR

------------------------------------------------------------------------

`10.0.0.0 – 10.255.255.255` `/8` `172.16.0.0 – 172.31.255.255` `/12`
`192.168.0.0 – 192.168.255.255` `/16`

Private addresses are intended for internal networks and are not
directly routed across the public Internet.

Example:

``` text
Laptop
192.168.1.25
     ↓
NAT
     ↓
Public IP
203.0.113.50
     ↓
Internet
```

> `203.0.113.0/24` is a documentation/example range, not a normal public
> address range to use on the Internet.

------------------------------------------------------------------------

# 6.6 Network ID and Host ID

An IPv4 address can conceptually be divided into:

``` text
Network Portion | Host Portion
```

Example:

``` text
192.168.10.25/24
```

With `/24`:

``` text
Network = 192.168.10.0
Host    = .25
```

The subnet mask determines where the network portion ends.

------------------------------------------------------------------------

# 6.7 What is a Subnet Mask?

A **subnet mask** tells a device which bits represent the network and
which represent the host.

Example:

``` text
IP:
192.168.10.25

Mask:
255.255.255.0
```

Binary:

``` text
IP:
11000000.10101000.00001010.00011001

Mask:
11111111.11111111.11111111.00000000
```

The `1`s represent the network portion.

The `0`s represent the host portion.

------------------------------------------------------------------------

# 6.8 CIDR Notation

CIDR means **Classless Inter-Domain Routing**.

Instead of writing:

``` text
192.168.10.25
255.255.255.0
```

we can write:

``` text
192.168.10.25/24
```

The `/24` means:

``` text
24 network bits
8 host bits
```

------------------------------------------------------------------------

# 6.9 Common Subnet Masks and Host Counts

CIDR Subnet Mask Host Bits Usable Hosts

------------------------------------------------------------------------

`/8` 255.0.0.0 24 16,777,214 `/16` 255.255.0.0 16 65,534 `/24`
255.255.255.0 8 254 `/25` 255.255.255.128 7 126 `/26` 255.255.255.192 6
62 `/27` 255.255.255.224 5 30 `/28` 255.255.255.240 4 14 `/29`
255.255.255.248 3 6 `/30` 255.255.255.252 2 2

For ordinary IPv4 subnets:

``` text
Usable hosts = 2^(host bits) - 2
```

There are special cases such as `/31` point-to-point addressing and
`/32` host routes.

------------------------------------------------------------------------

# 6.10 Why Do We Subnet?

Suppose an organization has:

``` text
192.168.10.0/24
```

A `/24` provides:

``` text
254 usable host addresses
```

But suppose separate networks are required for:

``` text
HR
Finance
IT
Guest
Servers
```

Subnetting divides a larger network into smaller logical networks.

``` text
192.168.10.0/24
        │
        ├── Subnet A
        ├── Subnet B
        ├── Subnet C
        └── Subnet D
```

Benefits:

-   Smaller broadcast domains
-   Better address utilization
-   Easier routing
-   Network segmentation
-   Better troubleshooting
-   Better security architecture

------------------------------------------------------------------------

# 6.11 The Subnetting Progression

Memorize:

``` text
/24
 ↓
/25
 ↓
/26
 ↓
/27
 ↓
/28
 ↓
/29
 ↓
/30
```

As the prefix length increases:

``` text
Network bits ↑
Host bits   ↓
Subnet size ↓
Number of subnets ↑
```

------------------------------------------------------------------------

# 6.12 The Most Important Subnetting Skill: Block Size

For a mask whose interesting octet is not `255`:

``` text
Block Size = 256 - Mask Value
```

Example:

``` text
/26
255.255.255.192
```

Therefore:

``` text
256 - 192 = 64
```

Subnet boundaries:

``` text
0
64
128
192
```

So:

``` text
192.168.10.0/26
192.168.10.64/26
192.168.10.128/26
192.168.10.192/26
```

------------------------------------------------------------------------

# 6.13 /25 Subnetting Example

Network:

``` text
192.168.10.0/25
```

Mask:

``` text
255.255.255.128
```

Block size:

``` text
256 - 128 = 128
```

Subnets:

``` text
192.168.10.0/25
192.168.10.128/25
```

First subnet:

``` text
Network:
192.168.10.0

Usable:
192.168.10.1 – 192.168.10.126

Broadcast:
192.168.10.127
```

Second subnet:

``` text
Network:
192.168.10.128

Usable:
192.168.10.129 – 192.168.10.254

Broadcast:
192.168.10.255
```

------------------------------------------------------------------------

# 6.14 /26 Subnetting Example

Network:

``` text
192.168.20.0/26
```

Mask:

``` text
255.255.255.192
```

Block size:

``` text
256 - 192 = 64
```

Subnets:

``` text
192.168.20.0/26
192.168.20.64/26
192.168.20.128/26
192.168.20.192/26
```

Subnet Network Usable Range Broadcast

------------------------------------------------------------------------

1 192.168.20.0 .1 \-- .62 .63 2 192.168.20.64 .65 \-- .126 .127 3
192.168.20.128 .129 \-- .190 .191 4 192.168.20.192 .193 \-- .254 .255

------------------------------------------------------------------------

# 6.15 /27 Subnetting Example

Network:

``` text
192.168.30.0/27
```

Mask:

``` text
255.255.255.224
```

Block size:

``` text
256 - 224 = 32
```

Boundaries:

``` text
0
32
64
96
128
160
192
224
```

For:

``` text
192.168.30.64/27
```

we get:

``` text
Network:
192.168.30.64

Usable:
192.168.30.65 – 192.168.30.94

Broadcast:
192.168.30.95
```

------------------------------------------------------------------------

# 6.16 Fast Method to Find Network, Broadcast and Host Range

Given:

``` text
192.168.50.77/26
```

### Step 1 \-- Find mask

``` text
/26 = 255.255.255.192
```

### Step 2 \-- Find block size

``` text
256 - 192 = 64
```

### Step 3 \-- Find the range containing 77

``` text
0 – 63
64 – 127
128 – 191
192 – 255
```

`77` falls inside:

``` text
64 – 127
```

### Step 4 \-- Identify addresses

``` text
Network:
192.168.50.64

Broadcast:
192.168.50.127

Usable:
192.168.50.65 – 192.168.50.126
```

### Interview Shortcut

``` text
256 - 192 = 64
```

Then find the 64-sized range containing the host value.

------------------------------------------------------------------------

# 6.17 Network Address

The **network address** identifies the subnet itself.

Example:

``` text
192.168.10.25/24
```

Network address:

``` text
192.168.10.0
```

The network address is not normally assigned to an ordinary host.

------------------------------------------------------------------------

# 6.18 Broadcast Address

The **broadcast address** is the last address in an IPv4 subnet.

For:

``` text
192.168.10.0/24
```

Broadcast:

``` text
192.168.10.255
```

For:

``` text
192.168.10.64/26
```

Broadcast:

``` text
192.168.10.127
```

Routers normally do not forward ordinary directed IPv4 broadcasts
between interfaces.

------------------------------------------------------------------------

# 6.19 Default Gateway

The **default gateway** is the Layer 3 device a host uses when the
destination is outside its local subnet.

Example:

``` text
PC
IP:      192.168.10.25/24
Gateway: 192.168.10.1
```

Destination:

``` text
8.8.8.8
```

The PC determines:

``` text
8.8.8.8 is NOT in 192.168.10.0/24
```

Therefore:

``` text
PC
 ↓
192.168.10.1
 ↓
Router/Firewall
 ↓
Internet
```

If the destination is in the same subnet, the host does not need the
default gateway for that destination.

------------------------------------------------------------------------

# 6.20 Loopback Address

IPv4 loopback is:

``` text
127.0.0.0/8
```

The most commonly used address is:

``` text
127.0.0.1
```

It means:

> \"This machine itself.\"

It is useful for testing the local TCP/IP stack without sending traffic
onto the physical network.

------------------------------------------------------------------------

# 6.21 APIPA

APIPA uses:

``` text
169.254.0.0/16
```

A Windows host may automatically assign itself a `169.254.x.x` address
when it cannot obtain a DHCP lease.

Example:

``` text
PC
 ↓
DHCP Discover
 ↓
No DHCP response
 ↓
APIPA
169.254.x.x
```

Possible causes:

-   DHCP server unavailable
-   VLAN mismatch
-   Switch port issue
-   DHCP relay problem
-   Firewall/ACL blocking DHCP
-   Cable/Wi-Fi issue
-   DHCP scope exhaustion

Useful commands:

``` cmd
ipconfig /all
ipconfig /release
ipconfig /renew
```

------------------------------------------------------------------------

# 6.22 Default Route \-- 0.0.0.0/0

The default route is:

``` text
0.0.0.0/0
```

It means:

> \"Use this route when no more specific route matches the
> destination.\"

Example:

``` text
Destination      Next Hop
192.168.10.0/24  Direct
10.0.0.0/8       Router A
0.0.0.0/0        ISP
```

Traffic destined for:

``` text
8.8.8.8
```

can use the default route when no more specific route exists.

------------------------------------------------------------------------

# 6.23 Longest Prefix Match

Routers select the **most specific matching route**.

Example:

``` text
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
0.0.0.0/0
```

Destination:

``` text
10.10.10.25
```

The most specific match is:

``` text
10.10.10.0/24
```

Interview rule:

``` text
More specific route > less specific route
```

------------------------------------------------------------------------

# 6.24 Wildcard Mask

A wildcard mask is the inverse of a subnet mask.

Example:

``` text
Subnet Mask:
255.255.255.0

Wildcard:
0.0.0.255
```

Another example:

``` text
255.255.255.192
        ↓
0.0.0.63
```

Commonly encountered in:

-   Cisco ACLs
-   OSPF network statements
-   Routing configurations

Quick calculation:

``` text
Wildcard = 255.255.255.255 - Subnet Mask
```

------------------------------------------------------------------------

# 6.25 VLSM \-- Variable Length Subnet Masking

VLSM allows different subnet sizes to be used within the same larger
address space.

Suppose:

``` text
Network: 192.168.100.0/22
```

Requirements:

``` text
Finance → 100 hosts
HR      → 20 hosts
IT      → 300 hosts
```

Allocate the largest requirement first.

### IT \-- 300 Hosts

Need at least 300 usable addresses:

``` text
/23 → 510 usable hosts
```

Allocate:

``` text
192.168.100.0/23
```

### Finance \-- 100 Hosts

Need:

``` text
/25 → 126 usable hosts
```

Next available aligned block:

``` text
192.168.102.0/25
```

### HR \-- 20 Hosts

Need:

``` text
/27 → 30 usable hosts
```

Next available aligned block:

``` text
192.168.102.128/27
```

Result:

``` text
192.168.100.0/23   → IT
192.168.102.0/25   → Finance
192.168.102.128/27 → HR
```

VLSM strategy:

``` text
1. Sort requirements largest → smallest
2. Choose the smallest subnet that satisfies each requirement
3. Allocate sequentially
4. Check boundaries/alignment
5. Document network, usable range and broadcast
```

------------------------------------------------------------------------

# 6.26 Route Summarization

Route summarization combines multiple contiguous, properly aligned
networks into a shorter route advertisement.

Example:

``` text
10.1.0.0/24
10.1.1.0/24
10.1.2.0/24
10.1.3.0/24
```

These four aligned `/24` networks can be summarized as:

``` text
10.1.0.0/22
```

Instead of four routes:

``` text
10.1.0.0/24
10.1.1.0/24
10.1.2.0/24
10.1.3.0/24
```

we can advertise:

``` text
10.1.0.0/22
```

Benefits:

-   Smaller routing tables
-   Less routing information
-   Better scalability
-   Reduced routing-update overhead

------------------------------------------------------------------------

# 6.27 Subnetting and VLANs

A common enterprise design is:

``` text
             Layer 3 Switch / Firewall
                     |
       +-------------+-------------+
       |             |             |
     VLAN 10       VLAN 20       VLAN 30
       |             |             |
      HR          Finance          IT
```

Example:

    VLAN Department   Network           Gateway

------------------------------------------------------------------------

      10 HR           192.168.10.0/24   192.168.10.1
      20 Finance      192.168.20.0/24   192.168.20.1
      30 IT           192.168.30.0/24   192.168.30.1

A host in VLAN 10 communicating with a host in VLAN 20 requires Layer 3
routing.

``` text
HR PC
192.168.10.25
     |
     | VLAN 10
     ↓
L3 Gateway
192.168.10.1
     |
     | Routing
     ↓
192.168.20.1
     |
     | VLAN 20
     ↓
Finance PC
192.168.20.25
```

If a firewall performs inter-VLAN routing, firewall policies can
additionally control the traffic.

------------------------------------------------------------------------

# 6.28 IP Addressing in a Firewall Environment

Consider:

``` text
LAN
192.168.10.0/24
       |
       |
FortiGate
LAN: 192.168.10.1
WAN: DHCP/Public IP
       |
       |
Internet
```

A PC might have:

``` text
IP:      192.168.10.25
Mask:    255.255.255.0
Gateway: 192.168.10.1
DNS:     192.168.10.1 or another DNS server
```

For:

``` text
8.8.8.8
```

the host sends the frame toward:

``` text
192.168.10.1
```

The firewall performs routing and policy processing and may perform
source NAT before sending traffic to the Internet.

------------------------------------------------------------------------

# 6.29 Same-Subnet vs Different-Subnet Communication

### Same subnet

``` text
PC1
192.168.10.10/24
      |
      | ARP for destination
      ↓
PC2
192.168.10.20/24
```

The host can deliver locally at Layer 2.

### Different subnet

``` text
PC1
192.168.10.10/24
      |
      ↓
Default Gateway
192.168.10.1
      |
      ↓
Router
      |
      ↓
192.168.20.20
```

The host sends the frame to the MAC address of the gateway, not directly
to the remote host\'s MAC address.

------------------------------------------------------------------------

# 6.30 How Does a Host Decide Whether to Use the Gateway?

Suppose:

``` text
Host:
192.168.10.25/24

Gateway:
192.168.10.1
```

Destination A:

``` text
192.168.10.50
```

Decision:

``` text
Destination is local
        ↓
ARP for 192.168.10.50
        ↓
Send directly
```

Destination B:

``` text
192.168.20.50
```

Decision:

``` text
Destination is remote
        ↓
Use default gateway
        ↓
ARP for 192.168.10.1
        ↓
Send frame to gateway
```

This decision is fundamental to understanding packet flow.

------------------------------------------------------------------------

# 6.31 Troubleshooting Scenario \-- Internet Not Working

User reports:

> \"My PC has no Internet.\"

Do not immediately blame the ISP.

### Step 1 \-- Check IP Configuration

``` cmd
ipconfig /all
```

Check:

``` text
IPv4 Address
Subnet Mask
Default Gateway
DNS Servers
```

If you see:

``` text
169.254.x.x
```

investigate DHCP/connectivity first.

### Step 2 \-- Test Loopback

``` cmd
ping 127.0.0.1
```

If this fails, investigate the local TCP/IP stack/system.

### Step 3 \-- Test Gateway

``` cmd
ping 192.168.10.1
```

If this fails, investigate:

``` text
PC
 ↓
Switch/Wi-Fi
 ↓
VLAN
 ↓
Gateway
```

### Step 4 \-- Test Internet by IP

``` cmd
ping 8.8.8.8
```

If the gateway works but this fails, investigate:

-   Routing
-   Firewall policy
-   NAT
-   WAN/ISP
-   Upstream connectivity

### Step 5 \-- Test DNS

``` cmd
nslookup google.com
```

If:

``` text
8.8.8.8 works
```

but:

``` text
google.com does not resolve
```

DNS is a strong suspect.

------------------------------------------------------------------------

# 6.32 Troubleshooting APIPA

Symptom:

``` text
IPv4:
169.254.25.10
```

Likely chain:

``` text
PC
 ↓
DHCP Discover
 ↓
No DHCP Offer
 ↓
APIPA
```

Investigate:

``` text
1. Physical/Wi-Fi connectivity
2. Switch port
3. VLAN assignment
4. DHCP server
5. DHCP relay
6. Firewall/ACL
7. DHCP scope exhaustion
```

------------------------------------------------------------------------

# 6.33 Troubleshooting Cross-Subnet Communication

Scenario:

``` text
PC:
192.168.10.25/24

Server:
192.168.20.50/24
```

PC cannot reach server.

Check:

``` text
PC IP
   ↓
Subnet mask
   ↓
Default gateway
   ↓
Gateway interface/VLAN
   ↓
Routing table
   ↓
Firewall policy
   ↓
Return route
   ↓
Server-side firewall
```

A common mistake is checking only the forward path.

Remember:

``` text
Request path
     ↓
Destination
     ↓
Return path
```

Both directions matter.

------------------------------------------------------------------------

# 6.34 Subnetting Troubleshooting Flow

``` text
                START
                  |
                  ↓
          Check IP address
                  |
          +-------+-------+
          |               |
       Correct          APIPA
          |               |
          |          Check DHCP/VLAN
          ↓
      Check mask
          |
          ↓
   Determine subnet
          |
          ↓
  Check default gateway
          |
          ↓
   Same subnet?
      /           YES        NO
     |          |
     ↓          ↓
  ARP/L2     Gateway
  checks     /routing
     |          |
     +-----+----+
           |
           ↓
      Firewall policy
           |
           ↓
       Return path
           |
           ↓
        SUCCESS
```

------------------------------------------------------------------------

# 6.35 Useful Commands

## Windows

``` cmd
ipconfig
ipconfig /all
ipconfig /release
ipconfig /renew
ping 127.0.0.1
ping <gateway>
ping <destination>
tracert 8.8.8.8
nslookup google.com
route print
arp -a
```

## Linux

``` bash
ip addr
ip route
ip neigh
ping <gateway>
traceroute 8.8.8.8
```

## Network/Firewall Perspective

Verify:

``` text
IP address
Subnet mask/prefix
Gateway
ARP/neighbor table
Routing table
VLAN
Firewall policy
NAT
Return route
```

------------------------------------------------------------------------

# 6.36 Common Interview Questions

## Q1. What is an IP address?

An IP address is a logical Layer 3 address used to identify a host or
interface and enable communication across IP networks.

## Q2. What is the difference between public and private IP addresses?

Private IP ranges are intended for internal networks and are not
directly routed on the public Internet. Common RFC 1918 ranges are:

``` text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Public addresses are globally routable addresses used for Internet
communication.

## Q3. What is a subnet mask?

A subnet mask identifies which bits of an IPv4 address represent the
network portion and which represent the host portion.

Example:

``` text
192.168.10.25/24
```

means:

``` text
24 network bits
8 host bits
```

## Q4. How many usable hosts are in a /24?

``` text
Host bits = 32 - 24 = 8
2^8 = 256
256 - 2 = 254
```

Answer:

``` text
254 usable hosts
```

## Q5. How many usable hosts are in a /27?

``` text
Host bits = 32 - 27 = 5
2^5 = 32
32 - 2 = 30
```

Answer:

``` text
30 usable hosts
```

## Q6. What is the network address of 192.168.1.70/26?

``` text
/26
Mask = 255.255.255.192

Block size:
256 - 192 = 64

Ranges:
0–63
64–127
128–191
192–255
```

`70` falls in `64–127`.

Answer:

``` text
192.168.1.64
```

## Q7. What is the broadcast address of 192.168.1.70/26?

Network:

``` text
192.168.1.64
```

Next subnet:

``` text
192.168.1.128
```

Therefore:

``` text
192.168.1.127
```

## Q8. What is the default gateway?

The default gateway is the Layer 3 next hop a host uses when the
destination is outside its local subnet.

## Q9. What is APIPA?

APIPA is the IPv4 link-local range `169.254.0.0/16`. On systems such as
Windows, a `169.254.x.x` address commonly indicates that the host failed
to obtain a DHCP address.

## Q10. What is 0.0.0.0/0?

It is the IPv4 default route. It matches destinations for which there is
no more specific route.

## Q11. What is VLSM?

VLSM allows different subnet sizes to be allocated within the same
address space according to the number of hosts required.

## Q12. What is route summarization?

Route summarization combines multiple contiguous, properly aligned
networks into a larger aggregate prefix to reduce routing information.

------------------------------------------------------------------------

# Interview Question \-- Explain /24, /25, /26, /27

A strong answer:

``` text
/24 = 255.255.255.0   = 254 usable hosts
/25 = 255.255.255.128 = 126 usable hosts
/26 = 255.255.255.192 = 62 usable hosts
/27 = 255.255.255.224 = 30 usable hosts
```

As the prefix gets larger:

``` text
Network bits increase
Host bits decrease
Subnet size decreases
```

------------------------------------------------------------------------

# Interview Question \-- Why Can\'t a Host Directly Send to a Remote Subnet?

Ethernet delivery is local to the Layer 2 network.

For a remote destination:

``` text
PC
192.168.10.25
     |
     | Destination is remote
     ↓
Default Gateway
192.168.10.1
     |
     ↓
Router
     |
     ↓
Remote Network
192.168.20.0/24
```

The host uses the gateway\'s MAC address for the local Ethernet frame
while the IP packet retains the remote destination IP.

------------------------------------------------------------------------

# Interview Question \-- What Happens When the Subnet Mask Is Wrong?

A wrong subnet mask can cause a host to make an incorrect
local-vs-remote decision.

Example:

``` text
Host:
192.168.10.25/24
```

If incorrectly configured as:

``` text
192.168.10.25/16
```

the host may believe many `192.168.x.x` destinations are local when they
are actually remote.

This can lead to:

``` text
ARP failures
Incorrect traffic delivery
Communication failures
Routing problems
```

------------------------------------------------------------------------

# 3\--4 Minute Interview Answer

> \"An IP address is a logical Layer 3 address used to identify a device
> or interface and enable communication across networks. In IPv4, an
> address contains 32 bits and is divided into a network portion and a
> host portion using a subnet mask or CIDR prefix.
>
> For example, 192.168.10.25/24 has 24 network bits and 8 host bits.
> That gives 256 total addresses, normally 254 usable host addresses
> after excluding the network and broadcast addresses.
>
> Subnetting allows us to divide a larger network into smaller networks.
> Common prefixes include /24, /25, /26, /27 and /28. As the prefix
> becomes longer, the number of host addresses decreases while the
> number of available subnets increases.
>
> One important subnetting technique is block size. For example, a /26
> has the mask 255.255.255.192, so the block size is 256 minus 192,
> which is 64. The subnet boundaries are therefore 0, 64, 128 and 192.
> If I am given 192.168.10.77/26, the network is 192.168.10.64, the
> broadcast is 192.168.10.127, and the usable host range is
> 192.168.10.65 through 192.168.10.126.
>
> The default gateway is used when the destination is outside the
> host\'s local subnet. If the destination is local, the host can use
> ARP and communicate directly at Layer 2. If the destination is remote,
> the host resolves the gateway\'s MAC address and sends the Ethernet
> frame to the gateway.
>
> In enterprise networks, subnetting is commonly combined with VLANs.
> For example, HR can use 192.168.10.0/24, Finance 192.168.20.0/24, and
> IT 192.168.30.0/24. A Layer 3 switch or firewall can route between
> those VLANs and enforce security policies.
>
> For larger environments, VLSM allows different subnet sizes based on
> actual host requirements, while route summarization reduces multiple
> routes into a single aggregate prefix.
>
> From a troubleshooting perspective, I first verify the IP address,
> subnet mask, default gateway and VLAN. If I see a 169.254.x.x APIPA
> address, I investigate DHCP and Layer 2 connectivity. Then I test the
> local stack, gateway, remote IP connectivity and DNS separately. I
> also verify routing, firewall policy, NAT and the return path when
> traffic crosses networks.
>
> So, understanding IP addressing is not just about memorizing subnet
> masks. It is about understanding how a host decides whether traffic is
> local or remote and how routers and firewalls move that traffic
> between networks.\"

------------------------------------------------------------------------

# Memory Trick 🧠

Remember:

``` text
IP = WHERE
MAC = WHO
Subnet Mask = WHICH NETWORK
Gateway = HOW OUT
Route = WHERE NEXT
```

Subnetting:

``` text
256 - Mask = Block Size
```

Host count:

``` text
2^(Host Bits) - 2
```

------------------------------------------------------------------------

# ⭐ Gold Interview Tip

If an interviewer gives you an IP and CIDR, **do not guess**.

Use:

``` text
1. Convert CIDR → mask
2. Find the interesting octet
3. Calculate block size
4. Find the subnet range
5. Identify network address
6. Identify broadcast address
7. Identify usable range
```

Example:

``` text
172.16.45.130/27

/27
↓
255.255.255.224

256 - 224 = 32

Ranges:
0–31
32–63
64–95
96–127
128–159
160–191
...
```

Therefore:

``` text
Network:
172.16.45.128

Usable:
172.16.45.129 – 172.16.45.158

Broadcast:
172.16.45.159
```

**Say the calculation out loud in an interview.** It demonstrates
understanding rather than memorization.

------------------------------------------------------------------------

# Quick Revision Notes

``` text
IPv4
→ 32 bits

IPv6
→ 128 bits

Private IPv4
→ 10.0.0.0/8
→ 172.16.0.0/12
→ 192.168.0.0/16

Loopback
→ 127.0.0.0/8
→ commonly 127.0.0.1

APIPA
→ 169.254.0.0/16

Default Route
→ 0.0.0.0/0

/24
→ 254 usable hosts

/25
→ 126 usable hosts

/26
→ 62 usable hosts

/27
→ 30 usable hosts

/28
→ 14 usable hosts

/29
→ 6 usable hosts

/30
→ 2 usable hosts

Block Size
→ 256 - mask value

Host Formula
→ 2^host bits - 2

VLSM
→ Different subnet sizes

Summarization
→ Combine aligned networks

Gateway
→ Used for remote destinations

Longest Prefix Match
→ Most specific route wins
```

------------------------------------------------------------------------

# 🎯 Assignment \-- Test Yourself

Try answering these **without looking at the answers**.

### Question 1

How many usable hosts are in:

``` text
192.168.1.0/26
```

### Question 2

Find the following for:

``` text
192.168.10.75/27
```

-   Network address
-   Broadcast address
-   First usable
-   Last usable

### Question 3

What is the subnet mask for:

``` text
/28
```

### Question 4

What is the block size of:

``` text
255.255.255.224
```

### Question 5

Find the network address:

``` text
10.20.30.145/28
```

### Question 6

Find the broadcast address:

``` text
172.16.50.70/26
```

### Question 7

A PC has:

``` text
IP:      192.168.10.25
Mask:    255.255.255.0
Gateway: 192.168.20.1
```

What is wrong?

### Question 8

A PC receives:

``` text
169.254.50.20
```

What should you investigate first?

### Question 9

Explain the difference between:

``` text
192.168.10.0/24
192.168.10.0/25
```

### Question 10

Why does a host need a default gateway for communication with another
subnet?

### Question 11

Which route wins?

``` text
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
```

Destination:

``` text
10.10.10.50
```

### Question 12

Design subnets for:

``` text
IT      → 300 hosts
Finance → 100 hosts
HR      → 20 hosts
```

Use VLSM and choose appropriate prefixes.

------------------------------------------------------------------------

# 🧪 Practical Assignment \-- Lab Practice

## Lab 1 \-- Windows IP Investigation

Run:

``` cmd
ipconfig /all
arp -a
route print
```

Record:

``` text
IPv4:
Subnet Mask:
Default Gateway:
DNS:
```

Explain what each table contains.

## Lab 2 \-- Subnetting Drill

For each IP, calculate:

``` text
Network
Broadcast
First Host
Last Host
```

### A

``` text
192.168.1.55/26
```

### B

``` text
192.168.1.130/26
```

### C

``` text
10.10.10.100/27
```

### D

``` text
172.16.20.200/28
```

## Lab 3 \-- Packet Decision

Given:

``` text
PC:
192.168.10.25/24

Gateway:
192.168.10.1
```

Determine whether the PC uses direct Layer 2 delivery or the default
gateway for:

``` text
192.168.10.50
192.168.20.50
8.8.8.8
```

Explain why for each destination.

## Lab 4 \-- Troubleshooting Simulation

Create:

``` text
PC
192.168.10.25/24
Gateway 192.168.10.1

        |
      Switch
        |
     Firewall
        |
     Internet
```

Simulate:

``` text
1. Wrong subnet mask
2. Wrong gateway
3. DHCP failure
4. Missing route
5. Firewall policy block
6. DNS failure
```

For each failure, identify:

``` text
Symptom
Test
Expected result
Likely cause
Fix
```

------------------------------------------------------------------------

# Interview-Ready Subnetting Cheat Sheet

``` text
CIDR     Mask                  Hosts     Block Size
---------------------------------------------------
/24      255.255.255.0          254          256
/25      255.255.255.128        126          128
/26      255.255.255.192         62           64
/27      255.255.255.224         30           32
/28      255.255.255.240         14           16
/29      255.255.255.248          6            8
/30      255.255.255.252          2            4
```

Remember:

``` text
Block Size = 256 - Mask
```

And:

``` text
Broadcast = Address immediately before next subnet
```

------------------------------------------------------------------------

# 🚀 Next Chapter

# **Chapter 7 \-- Switching Deep Dive**

In the next chapter, we move deeper into Layer 2 switching and
enterprise switching.

We will cover:

``` text
Switching Fundamentals
        ↓
VLANs
        ↓
Access Ports
        ↓
Trunk Ports
        ↓
802.1Q
        ↓
Native VLAN
        ↓
DTP
        ↓
VTP
        ↓
EtherChannel
        ↓
LACP
        ↓
PAgP
        ↓
VLAN Hopping
        ↓
Switch Security
        ↓
Enterprise Switching Design
        ↓
Switch Troubleshooting
```

The bridge from this chapter is:

``` text
IP Addressing
      ↓
Subnetting
      ↓
VLAN Segmentation
      ↓
Switching
      ↓
Inter-VLAN Routing
      ↓
Firewall Policy
```

------------------------------------------------------------------------

# 🌐 Keep Wandering

You have now moved from understanding **how IP addresses identify
networks** to understanding **how subnet masks control network
boundaries**.

The journey continues:

``` text
TCP/IP
   ↓
Encapsulation
   ↓
Ethernet + MAC + ARP
   ↓
IP Addressing
   ↓
Subnetting + CIDR
   ↓
VLANs
   ↓
Switching
   ↓
Routing
   ↓
Firewalls
   ↓
Packet Capture
   ↓
Troubleshooting
   ↓
Real-World Network Security
```

**Don\'t just memorize the CIDR table. Learn to calculate it.**

That is the point where subnetting stops being a memorization topic and
becomes a real networking skill.
