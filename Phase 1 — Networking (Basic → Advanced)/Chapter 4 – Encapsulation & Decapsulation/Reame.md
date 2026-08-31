# Chapter 4 – Encapsulation, Decapsulation & Packet Flow (Basic to Advanced)

---

# What is Encapsulation?

**Encapsulation** is the process in which each networking layer adds its own protocol information to data as the data moves **down the network stack**.

Think of it like putting a letter inside multiple envelopes:

```text
Application Data
      ↓
TCP/UDP Header + Data
      ↓
IP Header + Segment/Datagram
      ↓
Ethernet Header/Trailer + Packet
      ↓
Bits / Signals
```

Each layer adds information required for communication.

---

# Why Do We Need Encapsulation?

When a device sends data across a network, different pieces of information are required at different stages.

For example:

- The application needs application-level information.
- TCP/UDP needs port information.
- IP needs source and destination IP addresses.
- Ethernet needs local source and destination MAC addresses.
- The physical network needs signals or transmitted bits.

Encapsulation allows every layer to add the information it needs without requiring the application to understand every lower-layer detail.

---

# What is Decapsulation?

**Decapsulation** is the reverse process.

When the receiving device receives the data, each layer processes the information belonging to that layer and passes the remaining payload upward.

```text
Bits
  ↓
Ethernet Frame
  ↓
IP Packet
  ↓
TCP Segment / UDP Datagram
  ↓
Application Data
```

---

# Encapsulation vs Decapsulation

| Encapsulation | Decapsulation |
| ------------- | ------------- |
| Sender side | Receiver side |
| Data moves down the stack | Data moves up the stack |
| Headers/control information are added | Headers/control information are processed |
| Application → Network Access | Network Access → Application |
| Creates frames/packets/segments | Recovers the original application data |

---

# Data Units at Each Layer

Different networking layers use different names for their Protocol Data Units (PDUs).

| Layer | PDU / Data Unit |
| ----- | --------------- |
| Application | Data |
| Transport | Segment (TCP) / Datagram (UDP) |
| Internet | Packet |
| Network Access | Frame |
| Physical | Bits / Signals |

A simplified view is:

```text
Application
    │
    ▼
   DATA
    │
    ▼
TCP SEGMENT / UDP DATAGRAM
    │
    ▼
IP PACKET
    │
    ▼
ETHERNET FRAME
    │
    ▼
BITS
```

> Terminology can vary between technologies and resources, but this is the common networking model used throughout this handbook.

---

# The Big Picture

Suppose your computer wants to access:

```text
https://www.example.com
```

The communication can be represented as:

```text
Application Data
       ↓
TCP / UDP
       ↓
IP
       ↓
Ethernet / Wi-Fi
       ↓
Physical Medium
```

At the destination, the process is reversed:

```text
Physical Medium
       ↓
Ethernet / Wi-Fi
       ↓
IP
       ↓
TCP / UDP
       ↓
Application
```

---

# 1. Application Layer – Creating the Data

The process begins with an application.

For example:

```text
Web Browser
     ↓
HTTPS Request
```

The application generates data that needs to be transmitted.

For example:

```text
GET / HTTP/1.1
Host: example.com
```

The application does not normally need to manually construct the Ethernet frame or IP packet.

Lower layers handle that work.

---

# 2. Transport Layer – Adding Port Information

The application data is passed to the Transport Layer.

If TCP is being used, TCP adds a header containing information such as:

- Source port
- Destination port
- Sequence number
- Acknowledgment number
- Flags
- Window information
- Checksum

Conceptually:

```text
TCP Header
+
Application Data
```

This creates a:

> **TCP Segment**

Example:

```text
Source Port:      51532
Destination Port: 443
```

The destination port identifies the service endpoint that should receive the traffic.

---

# TCP Segment

A simplified TCP segment looks like:

```text
+-----------------------+
|      TCP Header       |
+-----------------------+
|                       |
|   Application Data    |
|                       |
+-----------------------+
```

The TCP header provides transport-layer control information.

---

# UDP Datagram

If UDP is used instead:

```text
+-----------------------+
|      UDP Header       |
+-----------------------+
|                       |
|   Application Data    |
|                       |
+-----------------------+
```

This creates a:

> **UDP Datagram**

UDP has a much smaller and simpler header than TCP.

---

# 3. Internet Layer – Adding IP Information

The Transport Layer passes its segment or datagram to the Internet Layer.

IP adds information such as:

- Source IP address
- Destination IP address
- TTL / Hop Limit
- Protocol / Next Header
- Fragmentation-related information where applicable

Conceptually:

```text
IP Header
+
TCP Segment
```

This creates an:

> **IP Packet**

---

# IP Packet

A simplified IPv4 packet looks like:

```text
+-----------------------+
|       IP Header       |
+-----------------------+
|     TCP / UDP         |
|       Segment         |
+-----------------------+
|   Application Data    |
+-----------------------+
```

The IP header provides the logical addressing information needed to move the packet between networks.

---

# Source and Destination IP

For example:

```text
Source IP:
192.168.10.10

Destination IP:
8.8.8.8
```

The source and destination IP addresses generally remain associated with the end-to-end IP communication as the packet travels through routers.

> **Important:** NAT can change IP addressing information at a network boundary. Therefore, the addresses seen on one side of a NAT device may differ from those seen on the other side.

---

# 4. Network Access Layer – Creating the Frame

The IP packet is then passed to the Network Access Layer.

For Ethernet, the device creates an Ethernet frame.

The frame contains information such as:

- Destination MAC
- Source MAC
- EtherType
- IP packet
- Frame Check Sequence (FCS)

Conceptually:

```text
Ethernet Header
+
IP Packet
+
Ethernet Trailer
```

This creates an:

> **Ethernet Frame**

---

# Ethernet Frame

A simplified Ethernet frame looks like:

```text
+-------------------------+
| Destination MAC         |
+-------------------------+
| Source MAC              |
+-------------------------+
| EtherType               |
+-------------------------+
|                         |
|       IP Packet         |
|                         |
+-------------------------+
| FCS                     |
+-------------------------+
```

---

# MAC Address

A MAC address identifies a network interface at the local link layer.

Example:

```text
00:1A:2B:3C:4D:5E
```

When Ethernet is used, the frame is delivered using MAC addresses on the local network segment.

---

# Important Concept – MAC Changes Hop by Hop

One of the most important packet-flow concepts is:

> **MAC addressing is local to the current Layer 2 segment.**

Suppose:

```text
PC
 ↓
Switch
 ↓
Router
 ↓
Internet
 ↓
Server
```

The Ethernet frame used from the PC toward its default gateway contains:

```text
Source MAC      = PC MAC
Destination MAC = Default Gateway MAC
```

When the router forwards the IP packet onto another network, it creates a new Layer 2 frame appropriate for that next link.

Therefore:

```text
IP addresses
      ↓
Generally end-to-end, subject to NAT

MAC addresses
      ↓
Change at Layer 2 boundaries / each hop
```

This distinction is extremely important for troubleshooting.

---

# Default Gateway Concept

Suppose your PC has:

```text
IP Address:
192.168.10.10

Subnet Mask:
255.255.255.0

Default Gateway:
192.168.10.1
```

If the destination is:

```text
8.8.8.8
```

The PC determines that the destination is outside its local subnet.

Therefore, it sends the Ethernet frame to its default gateway.

The frame might look conceptually like:

```text
Source MAC:
PC MAC

Destination MAC:
Gateway MAC

Source IP:
192.168.10.10

Destination IP:
8.8.8.8
```

The router then forwards the packet toward the destination.

---

# ARP Before Ethernet Delivery

How does the PC learn the MAC address of the default gateway?

For IPv4, it can use:

> **ARP – Address Resolution Protocol**

Suppose:

```text
Gateway IP:
192.168.10.1
```

The PC knows the IP address but needs the gateway's MAC address.

It sends an ARP request:

```text
Who has 192.168.10.1?
Tell 192.168.10.10
```

The gateway responds with its MAC address.

Conceptually:

```text
PC
 |
 | ARP Request
 | "Who has 192.168.10.1?"
 ↓
Broadcast
 |
 ↓
Gateway
 |
 | ARP Reply
 | "192.168.10.1 is at AA:BB:CC:DD:EE:FF"
 ↓
PC
```

The PC can now build the Ethernet frame.

> IPv6 does not use ARP. IPv6 uses Neighbor Discovery Protocol (NDP), which operates through ICMPv6.

---

# Complete Encapsulation Example

Suppose:

```text
Client:
192.168.10.10

Server:
8.8.8.8

Application:
HTTPS
```

The encapsulation process can be represented as:

```text
APPLICATION
------------------------------------------------
HTTPS Request
------------------------------------------------
                ↓

TRANSPORT
------------------------------------------------
TCP Header
+
HTTPS Request
------------------------------------------------
                ↓

INTERNET
------------------------------------------------
IP Header
+
TCP Header
+
HTTPS Request
------------------------------------------------
                ↓

NETWORK ACCESS
------------------------------------------------
Ethernet Header
+
IP Header
+
TCP Header
+
HTTPS Request
+
Ethernet FCS
------------------------------------------------
                ↓

PHYSICAL
------------------------------------------------
Bits / Signals
------------------------------------------------
```

---

# Decapsulation at the Destination

At the receiving device:

```text
Bits
 ↓
Ethernet Frame
 ↓
IP Packet
 ↓
TCP Segment
 ↓
HTTPS Data
```

Each layer processes the information belonging to it.

---

# Complete End-to-End Packet Flow

Now let's follow a packet through a real network.

Suppose:

```text
Client PC
192.168.10.10

Destination:
8.8.8.8
```

Network:

```text
PC
 |
Switch
 |
FortiGate
 |
Router / ISP
 |
Internet
 |
Destination
```

---

# Step 1 – Application

The application generates traffic.

For example:

```text
DNS Query
```

or:

```text
HTTPS Request
```

---

# Step 2 – DNS Resolution

If the user enters:

```text
www.example.com
```

the system may first need to resolve the hostname into an IP address.

Conceptually:

```text
www.example.com
       ↓
DNS Resolver
       ↓
IP Address
```

The exact DNS process can involve local caches, recursive resolvers, authoritative DNS servers, and other components.

---

# Step 3 – Transport Layer

For HTTPS using TCP:

```text
Source Port:
51532

Destination Port:
443
```

TCP establishes a connection using:

```text
SYN
 ↓
SYN + ACK
 ↓
ACK
```

---

# Step 4 – Routing Decision

The client compares the destination IP with its local subnet.

Example:

```text
Client:
192.168.10.10/24

Destination:
8.8.8.8
```

Because `8.8.8.8` is outside:

```text
192.168.10.0/24
```

the client sends the traffic toward:

```text
Default Gateway:
192.168.10.1
```

---

# Step 5 – ARP

The client needs the gateway's MAC address.

It checks its ARP cache.

If no valid entry exists, it performs ARP.

```text
Who has 192.168.10.1?
```

The gateway responds.

---

# Step 6 – Ethernet Frame

The client creates an Ethernet frame:

```text
Source MAC:
Client MAC

Destination MAC:
Gateway MAC
```

Inside that frame is the IP packet:

```text
Source IP:
192.168.10.10

Destination IP:
8.8.8.8
```

---

# Step 7 – Switch Processing

The switch receives the Ethernet frame.

The switch examines:

> **Destination MAC address**

It checks its MAC address table.

Example:

```text
MAC Address           Port
--------------------------------
AA:AA:AA:AA:AA:AA     Gi1/0/1
BB:BB:BB:BB:BB:BB     Gi1/0/24
```

The switch forwards the frame toward the appropriate port.

---

# Step 8 – Firewall Processing

Suppose the next device is a FortiGate.

The FortiGate receives the frame on its interface.

A simplified conceptual flow can include:

```text
Ingress Interface
       ↓
Packet / Header Processing
       ↓
Routing Lookup
       ↓
Firewall Policy
       ↓
NAT, if configured
       ↓
Security Inspection, if configured
       ↓
Session / State Handling
       ↓
Forwarding
```

The exact FortiGate processing sequence can vary depending on configuration and enabled features.

---

# Step 9 – Routing

The firewall/router determines where the packet should go.

It checks the routing table.

Example:

```text
Destination:
8.8.8.8

Route:
0.0.0.0/0

Next Hop:
ISP Router
```

The device forwards the packet toward the next hop.

---

# Step 10 – New Layer 2 Frame

The router removes the old Layer 2 frame and creates a new Layer 2 frame for the outgoing interface.

Conceptually:

```text
Old Frame

PC MAC
   ↓
Gateway MAC
   ↓
IP Packet
```

becomes:

```text
New Frame

Gateway/Router MAC
   ↓
Next-Hop MAC
   ↓
Same IP Packet*
```

*Subject to routing, NAT, tunneling, fragmentation, security processing, and other features.

---

# Hop-by-Hop View

This is the key concept:

```text
PC → Switch → Firewall → Router → ISP → Internet
```

At each Layer 3 forwarding hop:

```text
Layer 2 Frame
      ↓
Removed / processed
      ↓
IP Packet
      ↓
Routing decision
      ↓
New Layer 2 Frame
```

Therefore, the Ethernet frame is normally **hop-by-hop**, while the IP packet is routed across multiple networks.

---

# Packet Flow Diagram

```text
+-------------+
|    Client   |
| 192.168.10.10
+------+------+
       |
       | Ethernet Frame
       ↓
+-------------+
|   Switch    |
+------+------+
       |
       | Ethernet Frame
       ↓
+-------------+
|  FortiGate  |
+------+------+
       |
       | New Ethernet Frame
       ↓
+-------------+
| ISP Router  |
+------+------+
       |
       | New Layer 2 Frame
       ↓
+-------------+
|  Internet   |
+------+------+
       |
       ↓
+-------------+
| Destination |
+-------------+
```

---

# What Changes at Every Router?

A router normally changes Layer 2 information as it forwards traffic.

The IP packet is processed for routing.

Some IP header fields can change during forwarding.

For IPv4:

- TTL is decremented.

For IPv6:

- Hop Limit is decremented.

If NAT is configured:

- Source and/or destination IP addresses can be translated.

If the packet is encapsulated into a tunnel:

- Additional headers can be added.

---

# TTL – Time To Live

IPv4 uses:

> **TTL – Time To Live**

TTL helps prevent packets from circulating indefinitely because of routing loops.

Example:

```text
TTL = 64
```

After one Layer 3 forwarding hop:

```text
TTL = 63
```

After another:

```text
TTL = 62
```

Eventually, if TTL reaches zero, the packet is discarded and an ICMP message may be generated.

IPv6 uses:

> **Hop Limit**

instead of TTL.

---

# Why Does Ping Show TTL?

When you run:

```text
ping 8.8.8.8
```

the response may contain a TTL-related value.

For example:

```text
Reply from 8.8.8.8:
bytes=32 time=20ms TTL=117
```

The received value can provide clues about the path and the originating system's initial TTL, but it should not be treated as a precise operating-system fingerprint by itself.

---

# MAC Address vs IP Address During Packet Flow

This is one of the most important interview concepts.

| MAC Address | IP Address |
| ----------- | ---------- |
| Layer 2 addressing | Layer 3 addressing |
| Local link delivery | Routing between networks |
| Used by switches | Used by routers |
| Changes hop by hop | Usually remains end-to-end |
| 48-bit Ethernet address | IPv4 = 32-bit / IPv6 = 128-bit |

Example:

```text
Client
IP: 192.168.10.10
MAC: AA:AA:AA:AA:AA:AA

Server
IP: 8.8.8.8
MAC: Remote network MAC
```

The client does not normally place the remote server's MAC address directly into its first Ethernet frame.

It uses the MAC address of the local next hop, usually the default gateway.

---

# Switching vs Routing During Packet Flow

## Switch

A switch primarily forwards Ethernet frames based on MAC addresses.

```text
MAC Address
     ↓
MAC Table
     ↓
Output Port
```

## Router

A router forwards IP packets based on destination IP and routing information.

```text
Destination IP
      ↓
Routing Table
      ↓
Next Hop / Interface
```

---

# Example – PC to Internet

```text
PC
 |
 | Destination IP = 8.8.8.8
 | Destination MAC = Gateway MAC
 ↓
Switch
 |
 ↓
FortiGate
 |
 | Routing Lookup
 | Policy Check
 | NAT
 ↓
ISP
 |
 ↓
Internet
 |
 ↓
8.8.8.8
```

---

# Example – PC to Same Subnet

Suppose:

```text
PC1:
192.168.10.10/24

PC2:
192.168.10.20/24
```

The destination is in the same subnet.

PC1 can resolve PC2's MAC address using ARP.

Then:

```text
Source MAC:
PC1 MAC

Destination MAC:
PC2 MAC

Source IP:
192.168.10.10

Destination IP:
192.168.10.20
```

A router is not required for this local delivery.

---

# Example – PC to Different Subnet

Suppose:

```text
PC1:
192.168.10.10/24

Server:
192.168.20.10/24

Gateway:
192.168.10.1
```

PC1 determines that the server is outside its local subnet.

Therefore:

```text
Destination IP:
192.168.20.10

Destination MAC:
Gateway MAC
```

The destination IP remains the server's IP, while the first-hop destination MAC is the gateway's MAC.

---

# Packet Capture Perspective

Tools such as Wireshark allow you to observe packet headers and protocol behavior.

A capture may show:

```text
Ethernet II
    ↓
Internet Protocol
    ↓
Transmission Control Protocol
    ↓
TLS
    ↓
Application Data
```

This mirrors the encapsulation process.

---

# Wireshark Example

A TCP HTTPS packet might conceptually contain:

```text
Frame
 └── Ethernet II
      └── IPv4
           └── TCP
                └── TLS
                     └── Application Data
```

You can inspect:

- Source MAC
- Destination MAC
- Source IP
- Destination IP
- Source TCP port
- Destination TCP port
- TCP flags
- Sequence numbers
- Acknowledgment numbers
- Window size
- TCP options

---

# Firewall Packet Flow Troubleshooting

Suppose a user reports:

> "I cannot access the Internet."

A structured troubleshooting process can follow the packet path.

```text
Client
  ↓
Switch
  ↓
Firewall
  ↓
Router
  ↓
ISP
  ↓
Internet
```

Check each stage.

---

# Step 1 – Physical Connectivity

Check:

- Link status
- Cable
- NIC
- Wi-Fi connection
- Interface state

---

# Step 2 – IP Configuration

Check:

```text
IP Address
Subnet Mask
Default Gateway
DNS
```

On Windows:

```text
ipconfig /all
```

---

# Step 3 – ARP

Check whether the client knows the gateway MAC.

Windows:

```text
arp -a
```

Linux:

```text
ip neigh
```

---

# Step 4 – Gateway Reachability

Test:

```text
ping 192.168.10.1
```

If the gateway cannot be reached, investigate:

- VLAN
- Switch port
- ARP
- IP configuration
- Local firewall
- Cabling/Wi-Fi
- Gateway interface

---

# Step 5 – IP Reachability

Test:

```text
ping 8.8.8.8
```

If this works but domain names fail, investigate DNS.

---

# Step 6 – DNS

Test:

```text
nslookup example.com
```

or:

```text
dig example.com
```

If DNS fails, investigate:

- DNS server configuration
- DNS reachability
- UDP/TCP 53
- DNS filtering
- Firewall policy
- DNS server availability

---

# Step 7 – TCP Connectivity

For HTTPS:

```text
TCP 443
```

A TCP connection requires:

```text
SYN
↓
SYN-ACK
↓
ACK
```

If the handshake fails, investigate:

- Routing
- Firewall policy
- NAT
- ACL
- Server availability
- Security inspection
- Asymmetric routing

---

# Step 8 – Application Layer

If:

```text
IP works
DNS works
TCP 443 works
```

but the application still fails, investigate:

- TLS negotiation
- Certificate problems
- HTTP response codes
- Proxy
- Application configuration
- Server-side problems

---

# Packet Flow Troubleshooting Model

A useful troubleshooting model is:

```text
Physical
   ↓
Ethernet / Wi-Fi
   ↓
ARP / Neighbor Discovery
   ↓
IP Addressing
   ↓
Routing
   ↓
Firewall Policy
   ↓
NAT
   ↓
TCP / UDP
   ↓
TLS / Application
```

The exact order can vary depending on the symptom.

---

# FortiGate Packet Flow Example

Suppose:

```text
LAN Client
192.168.10.10
       |
       |
       ↓
FortiGate
       |
       ↓
Internet
```

The user accesses:

```text
https://example.com
```

A simplified conceptual flow is:

```text
Client
  ↓
Ethernet Frame
  ↓
FortiGate Ingress
  ↓
Routing Decision
  ↓
Firewall Policy
  ↓
NAT
  ↓
Security Inspection
  ↓
Session Handling
  ↓
Egress Interface
  ↓
New Layer 2 Frame
  ↓
Internet
```

The exact FortiGate packet flow depends on configuration, FortiOS version, inspection mode, acceleration, and enabled security features.

---

# Important Firewall Concept – Session

Firewalls commonly maintain state about connections.

For example, a TCP session can be represented conceptually as:

```text
Source:
192.168.10.10:51532

Destination:
8.8.8.8:443

Protocol:
TCP
```

The firewall can track:

- Source
- Destination
- Ports
- Protocol
- State
- NAT information
- Policy/session information

This state helps the firewall make decisions about subsequent packets belonging to the same flow.

---

# Return Traffic

Packet flow is bidirectional.

Suppose:

```text
Client → Internet
```

The return traffic travels:

```text
Internet
   ↓
Firewall
   ↓
Switch
   ↓
Client
```

The firewall must correctly associate the returning traffic with the existing session.

Conceptually:

```text
Client
  │
  │ Request
  ▼
Firewall
  │
  ▼
Internet
  │
  │ Response
  ▼
Firewall
  │
  ▼
Client
```

---

# Asymmetric Routing

A major troubleshooting problem occurs when forward and return traffic use different paths.

Example:

```text
Forward:

Client
  ↓
Firewall A
  ↓
Internet
```

But return traffic:

```text
Internet
  ↓
Firewall B
  ↓
Client
```

A stateful firewall may not see both directions of the session.

This can cause:

- Session failures
- Dropped traffic
- Unexpected resets
- NAT problems
- Difficult troubleshooting

Therefore:

> **Always consider the return path when troubleshooting stateful networks.**

---

# Packet Flow vs Application Flow

It is important to distinguish:

### Application Flow

```text
Browser
 ↓
HTTPS
 ↓
Web Server
```

### Network Flow

```text
Client
 ↓
Switch
 ↓
Firewall
 ↓
Router
 ↓
ISP
 ↓
Internet
 ↓
Server
```

### Packet Flow

```text
Frame
 ↓
Packet
 ↓
Routing
 ↓
New Frame
 ↓
Next Hop
```

All three describe the same communication from different perspectives.

---

# Encapsulation with TCP

Complete example:

```text
Application Data
        ↓
+----------------------+
| TCP Header           |
| Source Port: 51532   |
| Destination: 443     |
+----------------------+
| Application Data     |
+----------------------+
        ↓
+----------------------+
| IP Header            |
| Src: 192.168.10.10   |
| Dst: 8.8.8.8         |
+----------------------+
| TCP Segment          |
+----------------------+
        ↓
+----------------------+
| Ethernet Header      |
| Src MAC              |
| Dst MAC              |
+----------------------+
| IP Packet             |
+----------------------+
| FCS                   |
+----------------------+
        ↓
       BITS
```

---

# Encapsulation with UDP

The same general process occurs with UDP:

```text
Application Data
        ↓
UDP Header + Data
        ↓
IP Header + UDP Datagram
        ↓
Ethernet Header + IP Packet + FCS
        ↓
Bits
```

The transport-layer behavior differs because UDP does not provide TCP-style connection establishment, sequencing, retransmission, or flow control.

---

# What Happens When a Packet Crosses a Router?

This is a very common interview question.

### Answer

When a router receives an IP packet:

1. It receives the Layer 2 frame.
2. It validates/processes the frame.
3. It extracts the IP packet.
4. It checks the destination IP.
5. It performs a routing lookup.
6. It decrements IPv4 TTL or IPv6 Hop Limit.
7. It performs any configured forwarding/NAT/security processing.
8. It creates an appropriate new Layer 2 frame.
9. It forwards the packet toward the next hop.

Conceptually:

```text
Old Frame
   ↓
Router
   ↓
IP Packet
   ↓
Routing Lookup
   ↓
New Frame
   ↓
Next Hop
```

---

# What Happens When a Packet Crosses a Switch?

A switch primarily processes the Layer 2 frame.

Conceptually:

```text
Incoming Frame
      ↓
Destination MAC
      ↓
MAC Address Table
      ↓
Output Port
      ↓
Outgoing Frame
```

A normal Layer 2 switch does not route the IP packet simply because it sees the destination IP.

---

# Broadcast vs Unicast During Packet Flow

## Unicast

One sender → One receiver.

```text
PC
 ↓
Server
```

## Broadcast

One sender → All devices in the local broadcast domain.

Example:

```text
ARP Request
```

Conceptually:

```text
PC
 ↓
Broadcast
 ↓
All devices in VLAN
```

Routers normally do not forward Layer 2 broadcasts between IP networks.

---

# Broadcast Domain

A broadcast domain is the set of devices that can receive a Layer 2 broadcast.

For example:

```text
VLAN 10
   ↓
Broadcast Domain
```

Routers and Layer 3 boundaries separate broadcast domains.

---

# Collision Domain

Modern switched Ethernet networks generally provide one collision domain per switch port in full-duplex operation.

Older hub-based networks could have a shared collision domain.

This distinction is important when understanding:

- Hubs
- Switches
- Ethernet
- Network performance
- Legacy networks

---

# Packet Flow and VLANs

Suppose:

```text
PC
 |
Switch
 |
VLAN 10
 |
Trunk
 |
FortiGate
```

A VLAN identifies a logical Layer 2 network.

An Ethernet frame may contain an 802.1Q VLAN tag when traversing a tagged trunk.

Conceptually:

```text
Ethernet Header
      +
802.1Q VLAN Tag
      +
IP Packet
```

The VLAN tag helps switches identify the logical VLAN associated with the frame on a trunk.

---

# Access Port vs Trunk

## Access Port

Typically carries traffic for one VLAN.

```text
PC
 |
Access Port
 |
VLAN 10
```

## Trunk Port

Can carry traffic for multiple VLANs using VLAN tagging.

```text
Switch
   ||
   || Trunk
   ||
FortiGate
```

Example:

```text
VLAN 10
VLAN 20
VLAN 30
```

---

# Inter-VLAN Routing

Suppose:

```text
VLAN 10
192.168.10.0/24

VLAN 20
192.168.20.0/24
```

A host in VLAN 10 communicating with VLAN 20 needs a Layer 3 routing function.

Conceptually:

```text
VLAN 10
   ↓
Layer 3 Gateway
   ↓
VLAN 20
```

The gateway can be provided by:

- Router
- Layer 3 switch
- Firewall

---

# Practical Packet Flow Example – VLAN + FortiGate

```text
PC
192.168.10.10
VLAN 10
   |
   | Access Port
   ↓
Switch
   |
   | 802.1Q Trunk
   ↓
FortiGate
   |
   | Routing / Policy / NAT
   ↓
Internet
```

This is a very common enterprise topology.

---

# Packet Capture – What Should You Check?

When troubleshooting a packet capture, examine the packet layer by layer.

## Ethernet

Check:

- Source MAC
- Destination MAC
- EtherType
- VLAN tag if present

## IP

Check:

- Source IP
- Destination IP
- Protocol
- TTL / Hop Limit
- Fragmentation fields where applicable

## TCP

Check:

- Source port
- Destination port
- SYN
- ACK
- FIN
- RST
- Sequence number
- Acknowledgment number
- Window
- Retransmissions

## UDP

Check:

- Source port
- Destination port
- Length
- Checksum

## Application

Check:

- DNS query/response
- HTTP status
- TLS handshake
- Application errors

---

# Common Packet Flow Failure Points

| Layer / Component | Possible Problem |
| ----------------- | ---------------- |
| Physical | Cable, link, NIC, Wi-Fi |
| Layer 2 | VLAN, MAC, switching |
| ARP/NDP | Neighbor resolution |
| Layer 3 | IP, subnet, routing |
| Firewall | Policy, session, inspection |
| NAT | Incorrect or missing translation |
| TCP/UDP | Port blocked, service unavailable |
| DNS | Resolution failure |
| TLS | Certificate/handshake problem |
| Application | Server/application issue |

---

# Troubleshooting Scenario

### Problem

User says:

> "The Internet is not working."

Do not immediately assume:

> "The firewall is blocking it."

Instead follow the packet path.

```text
1. Physical
      ↓
2. Link / VLAN
      ↓
3. IP Configuration
      ↓
4. ARP / Gateway
      ↓
5. Routing
      ↓
6. Firewall Policy
      ↓
7. NAT
      ↓
8. DNS
      ↓
9. TCP/UDP
      ↓
10. Application
```

This prevents random troubleshooting.

---

# Practical Commands

## Windows

### IP Configuration

```text
ipconfig /all
```

### ARP

```text
arp -a
```

### Routing Table

```text
route print
```

### Ping

```text
ping 8.8.8.8
```

### DNS

```text
nslookup example.com
```

### Path

```text
tracert 8.8.8.8
```

---

# Linux

### IP Configuration

```text
ip addr
```

### ARP / Neighbor Table

```text
ip neigh
```

### Routing Table

```text
ip route
```

### Ping

```text
ping 8.8.8.8
```

### DNS

```text
dig example.com
```

### Path

```text
traceroute 8.8.8.8
```

---

# FortiGate Troubleshooting Perspective

When traffic reaches a FortiGate, useful questions include:

1. Did the packet reach the expected interface?
2. What is the source IP?
3. What is the destination IP?
4. What protocol and port are being used?
5. Which route is selected?
6. Which firewall policy matches?
7. Is NAT configured?
8. Is the session created?
9. Is security inspection affecting the traffic?
10. Does the return traffic come back through the expected path?

These questions help convert:

> "Internet is not working"

into a specific technical problem.

---

# Interview Questions

## Q1. What is encapsulation?

**Answer:**

Encapsulation is the process of adding protocol information at each networking layer as data moves down the protocol stack.

---

## Q2. What is decapsulation?

**Answer:**

Decapsulation is the reverse process where the receiving system processes the protocol information at each layer and passes the remaining payload upward.

---

## Q3. What is the PDU of TCP?

**Answer:**

A TCP segment.

---

## Q4. What is the PDU of UDP?

**Answer:**

A UDP datagram.

---

## Q5. What is the PDU of IP?

**Answer:**

An IP packet.

---

## Q6. What is the PDU of Ethernet?

**Answer:**

An Ethernet frame.

---

## Q7. Do MAC addresses remain the same across the Internet?

**Answer:**

No. Ethernet MAC addressing is local to a Layer 2 segment. When a router forwards traffic, it normally creates a new Layer 2 frame for the next link.

---

## Q8. Do IP addresses change at every router?

**Answer:**

Normally, the source and destination IP addresses remain associated with the end-to-end packet as it crosses routers. However, features such as NAT can translate addresses, and other technologies such as tunneling can add or modify headers.

---

## Q9. What does a router do with an Ethernet frame?

**Answer:**

The router receives and processes the Layer 2 frame, extracts the IP packet, performs a routing lookup, applies relevant forwarding functions, and creates a new Layer 2 frame for the outgoing interface.

---

## Q10. What does a switch use to forward Ethernet frames?

**Answer:**

A Layer 2 switch primarily uses the destination MAC address and its MAC address table to determine the appropriate output port.

---

## Q11. What is ARP?

**Answer:**

ARP resolves an IPv4 address to a MAC address on the local network.

---

## Q12. Does IPv6 use ARP?

**Answer:**

No. IPv6 uses Neighbor Discovery Protocol (NDP), implemented through ICMPv6.

---

## Q13. Why does a PC use the gateway MAC instead of the remote server's MAC?

**Answer:**

Because MAC addresses are used for local Layer 2 delivery. If the destination is outside the local subnet, the PC sends the frame to its default gateway, which is the local next hop.

---

## Q14. What happens to TTL when a router forwards an IPv4 packet?

**Answer:**

The router decrements the IPv4 TTL by one. If TTL expires, the packet is discarded and an ICMP Time Exceeded message may be generated.

---

## Q15. What is the difference between switching and routing?

**Answer:**

Switching primarily forwards Layer 2 frames based on MAC addresses, while routing forwards Layer 3 packets based on destination IP addresses and routing information.

---

## Q16. What is a VLAN?

**Answer:**

A VLAN provides logical segmentation of a Layer 2 network. It creates a separate broadcast domain within a switched infrastructure.

---

## Q17. What is the difference between an access port and a trunk port?

**Answer:**

An access port typically carries traffic for one VLAN, while a trunk carries traffic for multiple VLANs using VLAN tagging.

---

## Q18. What is asymmetric routing?

**Answer:**

Asymmetric routing occurs when traffic takes different paths in the forward and return directions. This can create problems for stateful firewalls if both directions of a session are not seen by the same device.

---

## Q19. How would you troubleshoot a website that is not opening?

**Answer:**

I would follow the packet path from the client outward:

```text
Physical
↓
VLAN / Layer 2
↓
IP / Gateway
↓
ARP
↓
Routing
↓
Firewall Policy
↓
NAT
↓
DNS
↓
TCP 443
↓
TLS
↓
Application
```

This helps isolate the failing layer rather than assuming the firewall is responsible.

---

## Q20. Explain packet flow through a FortiGate.

**Answer:**

A simplified explanation is:

```text
Ingress Interface
      ↓
Packet Processing
      ↓
Routing Lookup
      ↓
Firewall Policy
      ↓
NAT, if configured
      ↓
Security Inspection, if configured
      ↓
Session Handling
      ↓
Egress Interface
      ↓
New Layer 2 Frame
```

The exact FortiGate packet-processing path depends on configuration and enabled features.

---

# Interview Answer – Explain Encapsulation in 3–4 Minutes

> **"Encapsulation is the process of adding protocol information as data moves down the networking stack. The application first creates data. At the Transport Layer, TCP or UDP adds transport information such as source and destination ports. With TCP, the result is a TCP segment. At the Internet Layer, IP adds source and destination IP addresses, creating an IP packet. At the Network Access Layer, Ethernet adds source and destination MAC addresses and other frame information, creating an Ethernet frame. Finally, the frame is transmitted as physical signals or bits. At the receiving device, decapsulation occurs in reverse. The Ethernet information is processed, then the IP information, then TCP or UDP, and finally the application receives the data. During routing, the Layer 2 frame normally changes at each hop, while the IP packet is forwarded between networks, subject to functions such as NAT or tunneling."**

---

# ⭐ Gold Interview Tip

Interviewers love asking:

> **"What changes when a packet passes through a router?"**

A strong answer is:

```text
Layer 2 Frame
      ↓
Processed by Router
      ↓
IP Packet
      ↓
TTL / Hop Limit updated
      ↓
Routing Decision
      ↓
New Layer 2 Frame
      ↓
Next Hop
```

And remember:

> **MAC = local hop**

> **IP = routed communication**

> **Port = application/service endpoint**

This three-level distinction is extremely important in networking interviews.

---

# Quick Revision Notes

- **Encapsulation** = Adding protocol information as data moves down the stack.
- **Decapsulation** = Processing/removing protocol information as data moves up the stack.
- **TCP PDU** = Segment.
- **UDP PDU** = Datagram.
- **IP PDU** = Packet.
- **Ethernet PDU** = Frame.
- **Physical transmission** = Bits/signals.
- **MAC addresses** are used for local Layer 2 delivery.
- **IP addresses** are used for Layer 3 routing.
- **Ports** identify transport-layer service endpoints.
- **ARP** resolves IPv4 addresses to MAC addresses on a local network.
- **IPv6 uses NDP**, not ARP.
- **Switches** primarily forward frames using MAC addresses.
- **Routers** forward packets using IP/routing information.
- **MAC addressing is hop-by-hop.**
- **IP addressing is generally end-to-end, subject to NAT and other mechanisms.**
- **TTL** is decremented by IPv4 routers.
- **IPv6 uses Hop Limit.**
- **VLANs** provide Layer 2 segmentation.
- **Access ports** typically carry one VLAN.
- **Trunks** carry multiple VLANs.
- **Stateful firewalls** track sessions.
- **Asymmetric routing** can cause stateful firewall problems.
- **Packet captures** let you inspect networking layer by layer.
- **Troubleshooting should follow the packet path.**

---

# 🎯 Assignment — Test Yourself

Try answering these **without looking back**:

### Beginner

1. What is encapsulation?
2. What is decapsulation?
3. What is the PDU at the Transport Layer when TCP is used?
4. What is the PDU at the Transport Layer when UDP is used?
5. What is an IP packet?
6. What is an Ethernet frame?
7. Explain the complete encapsulation process.
8. What information does TCP add to application data?
9. What information does IP add to a packet?
10. What information does Ethernet add to a frame?

### Understanding
    
12. Why does a PC use the gateway's MAC address when accessing a remote network?
13. What is ARP?
14. Does IPv6 use ARP? Explain.
15. Do MAC addresses remain the same across routers?
16. Do IP addresses always remain unchanged?
17. What happens to TTL when a router forwards an IPv4 packet?
18. What is the difference between switching and routing?
19. What is the difference between an access port and a trunk port?
20. What is a VLAN?
21. What is asymmetric routing?
22. Explain the packet flow from a PC to `8.8.8.8`.
23. What happens when a packet reaches a FortiGate?
24. How would you troubleshoot a user who cannot access a website?
25. What would you check in a Wireshark capture?
26. Explain encapsulation and decapsulation in 3–4 minutes as if you were in a networking interview.

---

# 🧪 Practical Assignment — Lab Practice

Build or simulate this topology:

```text
PC
 |
Switch
 |
FortiGate / Router
 |
Internet / Test Network
```

Use a simple addressing scheme such as:

```text
PC:
192.168.10.10/24

Gateway:
192.168.10.1/24
```

Then perform:

### Task 1 – Check IP Configuration

Windows:

```text
ipconfig /all
```

Linux:

```text
ip addr
```

Identify:

- IP address
- Subnet mask/prefix
- Default gateway
- DNS server

---

### Task 2 – Check ARP

Windows:

```text
arp -a
```

Linux:

```text
ip neigh
```

Find the MAC address associated with the gateway.

---

### Task 3 – Check Routing

Windows:

```text
route print
```

Linux:

```text
ip route
```

Identify:

> **0.0.0.0/0**

or the default route.

---

### Task 4 – Test Gateway

```text
ping 192.168.10.1
```

Record:

- Success/failure
- Response time
- Any packet loss

---

### Task 5 – Test IP Connectivity

```text
ping 8.8.8.8
```

Determine whether basic IP connectivity is working.

---

### Task 6 – Test DNS

```text
nslookup example.com
```

or:

```text
dig example.com
```

Determine whether DNS resolution works.

---

### Task 7 – Capture Traffic

Use Wireshark and identify:

```text
Ethernet
 ↓
IP
 ↓
TCP/UDP
 ↓
Application
```

Find:

- Source MAC
- Destination MAC
- Source IP
- Destination IP
- Source port
- Destination port
- TCP flags

---

### Task 8 – Follow a TCP Handshake

Capture a TCP connection and identify:

```text
SYN
 ↓
SYN-ACK
 ↓
ACK
```

Then identify the TCP connection termination if the capture contains it.

---

### Task 9 – Observe the Gateway MAC

Generate traffic to an external IP and compare:

```text
Destination IP
```

with:

```text
Destination MAC
```

Explain why the MAC address belongs to the local next hop rather than the remote Internet host.

---

### Task 10 – Explain the Complete Packet

Take one captured packet and explain it from:

```text
Application
   ↓
TCP / UDP
   ↓
IP
   ↓
Ethernet
   ↓
Physical
```

Then explain the same packet from the receiver's perspective:

```text
Physical
   ↓
Ethernet
   ↓
IP
   ↓
TCP / UDP
   ↓
Application
```

---

# 🚀 Next Chapter

After understanding:

```text
Encapsulation
Decapsulation
Packet Flow
MAC vs IP
Switching vs Routing
VLANs
Firewall Flow
```

the next major topic is:

# Chapter 5 – Ethernet, MAC Addressing & Switching Fundamentals

This chapter will build the foundation for:

- MAC address tables
- ARP tables
- Ethernet frames
- VLANs
- Access ports
- Trunk ports
- Broadcast domains
- Collision domains
- STP
- CAM tables
- Switch forwarding
- Unknown unicast
- Broadcast flooding
- MAC learning
- Layer 2 troubleshooting

The goal is to understand exactly what a **switch does with a frame** after receiving it.

---

# 🌐 Keep Wandering

> **Don't just know where the packet goes. Understand why it goes there.**

```text
Application
     ↓
Transport
     ↓
IP
     ↓
MAC
     ↓
Frame
     ↓
Physical
```

**Once you can follow a packet, networking starts making sense.**

