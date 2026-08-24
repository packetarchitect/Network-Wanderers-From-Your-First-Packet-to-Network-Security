# 🌐 Chapter 02 — OSI Model

> **Understand the seven layers of network communication and learn how to use the OSI model to understand and troubleshoot networks.**

---

# 1. What is the OSI Model?

The **OSI (Open Systems Interconnection) Model** is a **7-layer reference model** developed by the **ISO (International Organization for Standardization)**.

It provides a standardized way of thinking about how different systems communicate over a network.

### In simple words:

> The OSI model breaks network communication into **seven logical layers**, with each layer performing a specific role.

---

## 🎤 Interview Definition

> The OSI model is a conceptual framework that divides network communication into seven layers, where each layer has specific responsibilities and communicates with the layers immediately above and below it.

---

# 2. Why Do We Need the OSI Model?

Imagine:

- Company A manufactures computers.
- Company B manufactures switches.
- Company C manufactures routers.

Without common standards, different vendors could use completely different communication methods.

This could make interoperability difficult.

The OSI model provides a common reference framework for understanding network communication.

### Think of it like a common language.

```text
Company A
Computers
    │
    │
    ▼
 Common
 Standards
    ▲
    │
    │
Company B              Company C
Switches                Routers
```

The OSI model helps networking professionals understand **where a particular function belongs** and **where a problem may be occurring**.

---

# 3. The Seven OSI Layers

The OSI model contains seven layers.

| Layer | Name | Main Responsibility | PDU |
|------:|------|---------------------|-----|
| 7 | Application | User/application network services | Data |
| 6 | Presentation | Encryption, compression, formatting | Data |
| 5 | Session | Session management | Data |
| 4 | Transport | End-to-end delivery | Segment / Datagram |
| 3 | Network | Routing and logical addressing | Packet |
| 2 | Data Link | Switching and MAC addressing | Frame |
| 1 | Physical | Transmission of bits | Bits |

---

# 4. Easy Way to Remember the Layers

From **Layer 7 → Layer 1**:

```text
Application
Presentation
Session
Transport
Network
Data Link
Physical
```

### Mnemonic

> **All People Seem To Need Data Processing**

```text
A → Application
P → Presentation
S → Session
T → Transport
N → Network
D → Data Link
P → Physical
```

---

# 5. Another Way to Visualize the OSI Model

```text
┌───────────────────────────────┐
│ 7. Application                │
├───────────────────────────────┤
│ 6. Presentation               │
├───────────────────────────────┤
│ 5. Session                    │
├───────────────────────────────┤
│ 4. Transport                  │
├───────────────────────────────┤
│ 3. Network                    │
├───────────────────────────────┤
│ 2. Data Link                  │
├───────────────────────────────┤
│ 1. Physical                   │
└───────────────────────────────┘
```

A useful way to think about it:

```text
Higher Layers
     │
     │ Application / User Services
     │
     ▼
Transport
     │
     ▼
Network
     │
     ▼
Data Link
     │
     ▼
Physical
     │
     ▼
Actual transmission
```

---

# 6. Layer 7 — Application Layer

## Purpose

The Application Layer provides network services directly to applications and users.

### Important

Applications such as:

- Chrome
- Outlook
- File Explorer

are **not themselves the OSI Application Layer**.

Instead, they use **Application Layer protocols and services** to communicate over the network.

---

## Common Protocols

Examples include:

- HTTP
- HTTPS
- FTP
- SFTP
- SMTP
- POP3
- IMAP
- DNS
- DHCP
- SNMP
- SSH
- Telnet

---

## Real Example

You open:

```text
https://www.google.com
```

Your browser may use Application Layer protocols such as:

```text
HTTPS
DNS
```

DNS can be used to resolve the domain name to an IP address, while HTTPS is used for secure web communication.

---

## Troubleshooting Example

Suppose DNS resolution fails.

```text
google.com
     │
     X
   DNS fails
```

You may still be able to reach a known IP address:

```text
ping 8.8.8.8
```

This can help indicate that the underlying network connectivity may be working even though name resolution is not.

---

# 7. Layer 6 — Presentation Layer

## Purpose

The Presentation Layer is commonly associated with:

- Data formatting
- Encryption
- Decryption
- Compression

It deals with how data is represented or transformed so that communicating systems can interpret it.

---

## Real Example

When communicating with:

```text
https://bank.com
```

TLS can provide encryption for the communication.

```text
Application Data
       │
       ▼
Encryption / Encoding
       │
       ▼
Transmission
```

---

## Common Technologies / Formats

Examples commonly associated with this layer include:

- TLS
- SSL
- JPEG
- PNG
- MP3
- MPEG

> **Important:** In real-world TCP/IP networking, these functions do not always map neatly to a single OSI layer. The OSI model is a conceptual reference model.

---

## 🎤 Interview Question

### Which OSI layer is traditionally associated with encryption?

**Answer:**

Layer 6 — Presentation Layer.

---

# 8. Layer 5 — Session Layer

## Purpose

The Session Layer is responsible for:

- Establishing sessions
- Maintaining sessions
- Managing sessions
- Terminating sessions

A session represents an ongoing communication interaction between systems or applications.

---

## Real Example

Imagine you are participating in a Microsoft Teams meeting.

```text
Join Meeting
     │
     ▼
Session Established
     │
     ▼
Communication Continues
     │
     ▼
Leave Meeting
     │
     ▼
Session Ends
```

---

## Other Examples

Session concepts can be encountered with:

- Database sessions
- SMB sessions
- Remote Desktop sessions

---

# 9. Layer 4 — Transport Layer

The **Transport Layer** is one of the most important layers for networking and technical interviews.

It provides communication between applications running on different hosts.

---

## Responsibilities

Depending on the protocol, Transport Layer functions include:

- End-to-end delivery
- Segmentation
- Flow control
- Error recovery
- Reliability
- Port numbers

---

# 10. TCP

**TCP — Transmission Control Protocol**

TCP is:

- Connection-oriented
- Reliable
- Sequenced
- Acknowledgment-based

TCP can use acknowledgments, sequencing, and retransmissions to provide reliable delivery.

### Examples

- HTTPS
- SSH
- FTP

---

## Simplified TCP Communication

```text
Sender                         Receiver
  │                               │
  │────── Data ──────────────────►│
  │                               │
  │◄───── ACK ────────────────────│
  │                               │
  │────── More Data ─────────────►│
```

---

# 11. UDP

**UDP — User Datagram Protocol**

UDP is:

- Connectionless
- Lightweight
- Fast
- Without TCP-style delivery guarantees

Examples where UDP may be used include:

- VoIP
- Video streaming
- DNS queries
- Online gaming

The choice between TCP and UDP depends on the application's requirements.

---

# 12. Port Numbers

The Transport Layer uses **port numbers** to identify applications or services.

Examples:

| Service | Port |
|---------|-----:|
| HTTP | 80 |
| HTTPS | 443 |
| SSH | 22 |
| FTP | 21 |
| DNS | 53 |
| DHCP | 67/68 |
| SMTP | 25 |
| SMB | 445 |

For example:

```text
Client
192.168.1.10
    │
    │ TCP Destination Port 443
    ▼
Web Server
```

Port `443` identifies HTTPS traffic at the transport layer.

---

## 🎤 Interview Question

### Why is TCP commonly used for banking applications?

**Answer:**

TCP provides reliable, ordered delivery using mechanisms such as acknowledgments, sequencing, and retransmissions.

> The important point is that the application chooses TCP when reliable transport is required. TCP itself does not provide banking security; encryption and application-level security are also required.

---

# 13. Layer 3 — Network Layer

This is the layer where **routing** occurs.

---

## Responsibilities

The Network Layer is responsible for functions such as:

- Logical addressing
- Routing
- Packet forwarding
- Path selection

---

## Address Used

The primary addressing system is the **IP address**.

Example:

```text
192.168.10.10
```

---

## Devices

Common Layer 3 devices include:

- Routers
- Layer 3 switches
- Firewalls with routing capabilities

---

## Protocols

Examples include:

- IPv4
- IPv6
- ICMP
- OSPF
- BGP
- RIP

---

# 14. Routing Example

Suppose a packet travels from Ahmedabad toward a destination in another location.

A simplified path might look like:

```text
Ahmedabad
    │
    ▼
Mumbai
    │
    ▼
Delhi
    │
    ▼
Destination
```

Routers use routing information to determine where packets should be forwarded.

> The actual Internet path can be very different and can change dynamically.

---

# 15. Layer 2 — Data Link Layer

The Data Link Layer is responsible for communication across the local data-link network.

This is where **Ethernet switching and MAC addressing** are commonly discussed.

---

## Responsibilities

- MAC addressing
- Frame forwarding
- Switching
- VLANs
- Error detection such as CRC

---

## Address Used

**MAC Address**

Example:

```text
00:11:22:33:44:55
```

---

## Devices

A Layer 2 switch is the most common example.

```text
PC1 ───┐
PC2 ───┤
PC3 ───┤── Switch
PC4 ───┘
```

---

## Technologies / Protocols

Examples include:

- Ethernet (IEEE 802.3)
- STP
- RSTP
- MSTP
- VLAN tagging (802.1Q)
- LACP

---

## 🎤 Interview Question

### How does a Layer 2 switch forward traffic?

**Answer:**

A Layer 2 switch uses its **MAC address table** to determine the appropriate outgoing interface for an Ethernet frame.

---

# 16. Layer 1 — Physical Layer

The Physical Layer is the lowest OSI layer.

It deals with the actual transmission of bits across the physical medium.

---

## Responsibilities

- Electrical signals
- Optical signals
- Radio signals
- Physical transmission
- Bit transmission

---

## Examples

Physical-layer components and technologies include:

- Copper cables
- Fiber-optic cables
- Wireless radio signals
- Patch panels
- Repeaters
- Hubs

---

## Data Unit

The PDU at Layer 1 is:

```text
Bits
```

Example:

```text
1010101010101010
```

---

# 17. OSI Layers — Complete Summary

| Layer | Name | Main Function | Address / Identifier | PDU |
|------:|------|---------------|----------------------|-----|
| 7 | Application | Network services | Application/service | Data |
| 6 | Presentation | Formatting/encryption/compression | — | Data |
| 5 | Session | Session management | — | Data |
| 4 | Transport | End-to-end transport | Port | Segment / Datagram |
| 3 | Network | Routing | IP Address | Packet |
| 2 | Data Link | Switching | MAC Address | Frame |
| 1 | Physical | Signal transmission | Physical medium | Bits |

---

# 18. Encapsulation

When data is sent from an application, it moves **down through the OSI layers**.

Each layer adds information needed by that layer.

This process is called **encapsulation**.

---

## Courier Analogy

Imagine sending a package through a company.

Each department adds information before the package is sent:

```text
Application
     │
     ▼
Presentation
     │
     ▼
Session
     │
     ▼
Transport
     │
     ▼
Network
     │
     ▼
Data Link
     │
     ▼
Physical
```

Each layer contributes information needed for communication.

---

# 19. Encapsulation and PDUs

A simplified encapsulation process looks like:

```text
Application
    │
    │ Data
    ▼
Transport
    │
    │ Segment / Datagram
    ▼
Network
    │
    │ Packet
    ▼
Data Link
    │
    │ Frame
    ▼
Physical
    │
    │ Bits
    ▼
Network Medium
```

---

# 20. Decapsulation

At the receiving side, the process happens in reverse.

The receiving system processes the information from the lower layers upward.

```text
Physical
    │
    ▼
Data Link
    │
    ▼
Network
    │
    ▼
Transport
    │
    ▼
Session
    │
    ▼
Presentation
    │
    ▼
Application
```

This process is called **decapsulation**.

The receiving system processes and removes the relevant headers/trailers as the data moves upward.

---

# 21. PDU — Protocol Data Unit

A **Protocol Data Unit (PDU)** is the name given to the data unit as it moves through a particular layer.

| Layer | PDU |
|------:|-----|
| Application | Data |
| Presentation | Data |
| Session | Data |
| Transport | Segment (TCP) / Datagram (UDP) |
| Network | Packet |
| Data Link | Frame |
| Physical | Bits |

### Easy way to remember:

```text
Data
  ↓
Segment / Datagram
  ↓
Packet
  ↓
Frame
  ↓
Bits
```

This is a very common networking interview topic.

---

# 22. Complete Example — Opening a Website

Suppose you open:

```text
https://www.google.com
```

A simplified OSI view looks like this:

---

## Layer 7 — Application

The browser generates an HTTPS request.

```text
HTTPS Request
```

---

## Layer 6 — Presentation

Encryption and data representation functions are handled here in the conceptual OSI model.

TLS provides encryption for HTTPS communication.

```text
Application Data
        ↓
    Encryption
```

---

## Layer 5 — Session

The communication session is established and maintained.

---

## Layer 4 — Transport

TCP adds transport information such as:

```text
Source Port
Destination Port: 443
```

The data becomes a TCP segment.

---

## Layer 3 — Network

An IP header is added.

Example:

```text
Source IP:
192.168.10.20

Destination IP:
142.x.x.x
```

The data is now carried inside an IP packet.

---

## Layer 2 — Data Link

An Ethernet frame is created.

The frame contains information such as:

```text
Source MAC
Destination MAC
```

---

## Layer 1 — Physical

The frame is converted into signals and transmitted as bits over:

- Copper
- Fiber
- Wireless

```text
101010101010101010
```

---

# 23. Complete Encapsulation Diagram

```text
                 Application Data
                        │
                        ▼
              ┌──────────────────┐
              │ Application       │
              └──────────────────┘
                        │
                        ▼
              ┌──────────────────┐
              │ Presentation     │
              └──────────────────┘
                        │
                        ▼
              ┌──────────────────┐
              │ Session          │
              └──────────────────┘
                        │
                        ▼
              ┌──────────────────┐
              │ TCP / UDP        │
              │ Segment/Datagram │
              └──────────────────┘
                        │
                        ▼
              ┌──────────────────┐
              │ IP Packet        │
              └──────────────────┘
                        │
                        ▼
              ┌──────────────────┐
              │ Ethernet Frame   │
              └──────────────────┘
                        │
                        ▼
                       Bits
                        │
                        ▼
                     Network
```

---

# 24. What Happens at the Receiver?

The receiving system processes the communication in the opposite direction.

```text
Bits
 │
 ▼
Frame
 │
 ▼
Packet
 │
 ▼
Segment / Datagram
 │
 ▼
Session
 │
 ▼
Presentation
 │
 ▼
Application Data
```

This is **decapsulation**.

---

# 25. OSI Devices

A simplified view of common devices:

| Device | Primary Layer |
|--------|---------------|
| Hub | Layer 1 |
| Layer 2 Switch | Layer 2 |
| Layer 3 Switch | Layer 3 |
| Router | Layer 3 |
| Firewall | Layer 3–7 depending on capabilities |
| Load Balancer | Layer 4 / Layer 7 depending on mode |
| Proxy | Layer 7 |

> Modern enterprise devices can operate across multiple layers. The table represents their commonly associated functions.

---

# 26. Troubleshooting Using the OSI Model

One of the most useful applications of the OSI model is **troubleshooting**.

Instead of randomly checking everything, you can work through the layers systematically.

---

## Layer 1 — Physical

Possible problems:

- Cable unplugged
- Bad cable
- Bad SFP
- Interface down
- No link light

Check:

```text
Cable
Interface
Link LEDs
SFP
Physical connectivity
```

---

## Layer 2 — Data Link

Possible problems:

- Incorrect VLAN
- VLAN mismatch
- STP blocking
- MAC learning problem
- Trunk problem

Check:

```text
VLAN
MAC Address Table
STP
Trunk
Switch Port
```

---

## Layer 3 — Network

Possible problems:

- Wrong IP address
- Incorrect subnet mask
- Wrong default gateway
- Routing problem

Check:

```text
IP Address
Subnet Mask
Gateway
ARP
Routing Table
Ping
```

---

## Layer 4 — Transport

Possible problems:

- Port blocked
- TCP connection failure
- TCP reset
- Service not listening

Check:

```text
TCP / UDP
Ports
Firewall Rules
Service Status
```

---

## Layer 5 — Session

Possible problems:

- Session timeout
- Session establishment failure
- Session termination

Check:

```text
Session State
Timeouts
Application Sessions
```

---

## Layer 6 — Presentation

Possible problems:

- TLS issue
- Certificate problem
- Encryption/decryption problem
- Data-format issue

Check:

```text
Certificate
TLS
Encryption
Supported Protocols
```

---

## Layer 7 — Application

Possible problems:

- DNS failure
- HTTP error
- Application crash
- Web server problem

Check:

```text
DNS
Application Logs
HTTP Response
Server Status
Application Configuration
```

---

# 27. Troubleshooting by Layer — Quick Table

| Layer | Example Problem |
|------:|-----------------|
| L1 | Cable unplugged, bad SFP |
| L2 | VLAN mismatch, STP blocking, MAC issue |
| L3 | Wrong IP, routing problem |
| L4 | Port blocked, TCP reset |
| L5 | Session timeout |
| L6 | TLS or certificate problem |
| L7 | DNS failure, HTTP error, application problem |

---

# 28. Real-World Troubleshooting Example

Imagine a user says:

> **"I cannot open the company website."**

Don't immediately assume the firewall is broken.

Use a structured approach.

---

## Layer 1

Check:

```text
Is the cable connected?
Is the interface up?
Is Wi-Fi connected?
```

---

## Layer 2

Check:

```text
Is the client in the correct VLAN?
Is the switch learning the MAC address?
Is the switch port working?
```

---

## Layer 3

Check:

```text
Does the client have an IP?
Is the subnet mask correct?
Is the default gateway correct?
Can the gateway be pinged?
Can the destination IP be reached?
```

---

## Layer 4

Check:

```text
Can TCP 443 be reached?
Is the firewall blocking the port?
Is the server listening?
```

---

## Layer 5

Check:

```text
Is the session being established?
Is the session timing out?
```

---

## Layer 6

Check:

```text
Is the TLS handshake working?
Is the certificate valid?
```

---

## Layer 7

Check:

```text
Does DNS resolve?
Is the web application working?
What HTTP response is returned?
Are there application errors?
```

This is much better than randomly changing configurations.

---

# 🎤 Interview Questions

## Q1. Which OSI layer uses IP addresses?

**Answer:**

Layer 3 — Network Layer.

---

## Q2. Which OSI layer uses MAC addresses?

**Answer:**

Layer 2 — Data Link Layer.

---

## Q3. Which OSI layer performs routing?

**Answer:**

Layer 3 — Network Layer.

---

## Q4. Which OSI layer is associated with switching?

**Answer:**

Layer 2 — Data Link Layer.

---

## Q5. Which OSI layer is traditionally associated with encryption?

**Answer:**

Layer 6 — Presentation Layer.

---

## Q6. Which OSI layer uses port numbers?

**Answer:**

Layer 4 — Transport Layer.

---

## Q7. What is the PDU of the Transport Layer?

**Answer:**

- TCP → Segment
- UDP → Datagram

---

## Q8. What is the PDU of the Network Layer?

**Answer:**

Packet.

---

## Q9. What is the PDU of the Data Link Layer?

**Answer:**

Frame.

---

## Q10. What is the PDU of the Physical Layer?

**Answer:**

Bits.

---

# ⭐ Gold Interview Question

### "A user cannot open a website. How would you troubleshoot it using the OSI model?"

A structured answer:

### Layer 1 — Physical

Check:

- Cable
- Link status
- Interface status
- Wi-Fi connection
- SFP

### Layer 2 — Data Link

Check:

- VLAN
- MAC address learning
- STP
- Trunk configuration

### Layer 3 — Network

Check:

- IP address
- Subnet mask
- Default gateway
- ARP
- Routing
- Ping

### Layer 4 — Transport

Check:

- TCP/UDP
- Port 80/443
- Firewall rules
- TCP connection

### Layer 5 — Session

Check:

- Session establishment
- Session timeout

### Layer 6 — Presentation

Check:

- TLS
- Certificate
- Encryption

### Layer 7 — Application

Check:

- DNS
- Web server
- HTTP response
- Application logs

---

# 🧠 Quick Revision

```text
L7 → APPLICATION
     User/application network services
     HTTP, HTTPS, DNS, SSH

L6 → PRESENTATION
     Encryption, formatting, compression
     TLS, data formats

L5 → SESSION
     Session establishment and management

L4 → TRANSPORT
     TCP / UDP
     Ports
     Segmentation
     Reliability

L3 → NETWORK
     IP
     Routing
     Packets

L2 → DATA LINK
     MAC
     Switching
     VLAN
     Frames

L1 → PHYSICAL
     Signals
     Cables
     Bits
```

---

# 🧩 The Three Most Important Addresses

Remember these three concepts:

```text
Layer 4
   │
   ▼
PORT
Identifies the application/service

Layer 3
   │
   ▼
IP ADDRESS
Identifies the Layer 3 destination

Layer 2
   │
   ▼
MAC ADDRESS
Identifies the Layer 2 interface/device
```

For example:

```text
PC
│
├── MAC → Layer 2
│
├── IP  → Layer 3
│
└── Port → Layer 4
```

These three concepts will become extremely important when we study:

- ARP
- Switching
- Routing
- NAT
- Firewall policies
- Firewall packet flow
- VPNs
- Network troubleshooting

---

# 🎯 Assignment — Test Yourself

Try answering these without looking back at the chapter.

### Beginner

1. What is the OSI model?
2. Why was the OSI model created?
3. How many layers does the OSI model have?
4. Name all seven layers.
5. What is the mnemonic for remembering the seven layers?
6. What does Layer 1 do?
7. What does Layer 2 do?
8. What does Layer 3 do?
9. What does Layer 4 do?

### Understanding

10. What is the difference between a MAC address and an IP address?
11. Why are port numbers needed?
12. What is a frame?
13. What is a packet?
14. What is a TCP segment?
15. What is a UDP datagram?
16. What is encapsulation?
17. What is decapsulation?
18. What is a PDU?
19. Why does a switch primarily operate at Layer 2?
20. Why does a router primarily operate at Layer 3?

### Troubleshooting

21. A user's network cable is disconnected. Which OSI layer is affected?

22. The user's IP address is incorrect. Which layer should you investigate?

23. The user's VLAN is incorrect. Which layer should you investigate?

24. Ping works, but HTTPS does not. Which layers would you investigate?

25. The website opens by IP address but not by domain name. What is a likely problem?

26. A website shows a TLS certificate error. Which OSI layer is traditionally associated with this problem?

27. A switch is not forwarding traffic to the correct port. What should you investigate?

---

# 🧠 Challenge — Think Like a Network Engineer

A user reports:

> **"My laptop is connected to Wi-Fi, but I cannot open https://example.com."**

Build your troubleshooting process from:

```text
L1
 ↓
L2
 ↓
L3
 ↓
L4
 ↓
L5
 ↓
L6
 ↓
L7
```

For every layer, write:

1. What could be wrong?
2. What would you check?
3. What command or tool could help?
4. What result would confirm or eliminate the problem?

---

# ✅ What You Should Understand Before Moving On

Before starting the next chapter, you should be able to explain:

- What the OSI model is
- Why it is useful
- All seven layers
- The primary purpose of each layer
- MAC vs IP vs Port
- TCP vs UDP
- Frame vs Packet vs Segment
- Encapsulation
- Decapsulation
- PDU
- Which layer performs switching
- Which layer performs routing
- How to troubleshoot using the OSI model

You do **not** need to memorize every protocol at this stage.

The important thing is to understand **what each layer is responsible for and how the layers work together.**

---

# 🚀 Next Chapter

## Chapter 03 — TCP/IP Model

The OSI model gives us a seven-layer conceptual framework.

Next, we will look at the **TCP/IP model**, which is the model more closely associated with the actual Internet protocol suite.

```text
Chapter 01
What is a Computer Network?
        ↓
Chapter 02
OSI Model
        ↓
Chapter 03
TCP/IP Model
        ↓
Chapter 04
Encapsulation & Decapsulation
        ↓
Chapter 05
Ethernet & Network Communication
```

---

# 🌐 Keep Wandering

> **Understand the layer. Understand the problem. Understand the network.**

**Your first packet is only the beginning.**
