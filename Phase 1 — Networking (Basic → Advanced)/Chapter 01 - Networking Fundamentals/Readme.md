## Chapter 1: What is a Computer Network?

### Definition

A **computer network** is a collection of two or more devices connected together so they can communicate and share resources.

These devices can include:

- Computers
- Servers
- Switches
- Routers
- Firewalls
- Printers
- IP Phones
- CCTV Cameras
- IoT Devices

---

## Why Do We Need Networking?

Without networking:

- No Internet
- No Email
- No File Sharing
- No Cloud
- No Video Calls
- No Banking Transactions

Imagine a company with 500 employees.

Without a network:

- Everyone would need to copy files using USB drives.
- Printers couldn't be shared.
- Email wouldn't work.
- ERP and CRM applications would be unavailable.

Networking solves these problems.

---

# Real-Life Example

Imagine a bank.

```
                 Internet
                     │
                FortiGate Firewall
                     │
                Core Switch
                 /        \
      Distribution    Distribution
          │               │
     Access Switch   Access Switch
        │                 │
     Employees         ATM Network
```

Every device communicates through the network.

---

# Components of a Network

## 1. End Devices (Hosts)

Devices that generate or receive data.

Examples:

- Laptop
- Desktop
- Mobile
- Server
- Printer

---

## 2. Switch

Purpose:

- Connects devices within the same LAN.
- Works mainly at **Layer 2**.
- Uses **MAC addresses** to forward frames.

Example:  
One office floor with 40 computers connected to one switch.

---

## 3. Router

Purpose:

- Connects different networks.
- Uses **IP addresses**.
- Chooses the best path using routing tables.

Example:  
Connecting the office LAN to the Internet.

---

## 4. Firewall

Purpose:

- Inspects and controls traffic.
- Enforces security policies.
- Allows or blocks traffic based on rules.

Example:  
Allow HTTPS (443), block Telnet (23).

---

## 5. Access Point (AP)

Purpose:

- Provides Wi-Fi connectivity.
- Bridges wireless devices to the wired network.

---

# Types of Networks

## PAN (Personal Area Network)

Example:  
Bluetooth headphones connected to your phone.

Range:  
~10 meters

---

## LAN (Local Area Network)

Example:  
Office network within one building.

```
PC ── Switch ── Printer
```

---

## MAN (Metropolitan Area Network)

Example:  
Connecting multiple offices across Ahmedabad.

---

## WAN (Wide Area Network)

Example:  
Connecting:

- Ahmedabad Office
- Mumbai Office
- Delhi Office

using MPLS or VPN.

The Internet is the largest WAN.

---

# Basic Communication Flow

Suppose:

```
PC A wants to open www.google.com
```

Communication:

```
PC

↓

Switch

↓

Firewall

↓

Router

↓

ISP

↓

Google Server
```

The response follows the reverse path.

---

# Types of Communication

## Unicast

One sender → One receiver

Example:  
You open Gmail.

```
PC  ─────► Server
```

---

## Broadcast

One sender → Everyone in the subnet

Example:  
ARP Request

```
PC

↓

Everyone in VLAN
```

IPv4 Broadcast Address:

```
255.255.255.255
```

---

## Multicast

One sender → Selected group of receivers

Example:  
IPTV  
Video conferencing  
Stock market feeds

Not every device receives the traffic.

---

# Network Devices by OSI Layer

|Device|Primary OSI Layer|
|---|---|
|Hub|Layer 1|
|Switch|Layer 2|
|Layer 3 Switch|Layer 3|
|Router|Layer 3|
|Firewall|Layer 3–7 (depending on type)|
|Access Point|Layer 2|

---

# How Does Data Travel?

Suppose:

PC1 wants to send a file to PC2.

```
PC1

↓

Switch

↓

Firewall (if crossing security zones)

↓

Router (if another network)

↓

Switch

↓

PC2
```

Each device performs a different role:

- Switch → forwards based on MAC.
- Router → forwards based on IP.
- Firewall → applies security policy.

---

# 🎤 Interview Questions

The following questions can be used for revision and interview preparation.

---

## Q1. What is a network?

### Answer

A network is a group of interconnected devices that communicate and share resources using standard communication protocols.

---

## Q2. What is the difference between LAN and WAN?

|LAN|WAN|
|---|---|
|Covers a relatively small geographical area|Covers a large geographical area|
|Commonly used in homes, offices, buildings, etc.|Used to connect geographically separated networks|
|Usually managed by an organization or individual|May involve ISPs or service providers|
|Example: Office network|Example: Connecting offices in different cities|

---

## Q3. Why is a router needed?

A router connects different IP networks and forwards packets based on routing information.

A Layer 2 switch primarily forwards Ethernet frames within a Layer 2 network using MAC addresses.

---

## Q4. Why is a firewall needed?

A firewall helps protect networks by inspecting and controlling traffic according to configured security policies.

Depending on its capabilities, a firewall may also provide features such as:

- NAT
- VPN
- Application inspection
- Intrusion prevention
- Web filtering
- Logging

---

## Q5. What is the difference between a switch and a router?

|Switch|Router|
|---|---|
|Primarily operates at Layer 2|Primarily operates at Layer 3|
|Uses MAC addresses for Layer 2 forwarding|Uses IP addresses for Layer 3 forwarding|
|Connects devices within a LAN|Connects different IP networks|
|Forwards Ethernet frames|Forwards IP packets|

---

## Q6. What is the difference between unicast, broadcast, and multicast?

**Unicast:** One sender → One receiver.

**Broadcast:** One sender → All relevant devices within the broadcast domain.

**Multicast:** One sender → A specific group of receivers.

---

# ⚠️ Common Beginner Mistakes

## ❌ "A switch sends packets."

A Layer 2 switch primarily forwards **Ethernet frames**.

---

## ❌ "A router uses MAC addresses to route traffic."

A router primarily makes Layer 3 forwarding decisions using **IP addresses and routing information**.

MAC addresses are still involved in the local Ethernet delivery of frames.

---

## ❌ "A firewall only blocks traffic."

A firewall can both **allow and block traffic** according to security policies.

Modern firewalls may also provide:

- NAT
- VPN
- Application inspection
- IPS
- Web filtering
- Logging

---

## ❌ "The Internet is one network."

The Internet is a **global interconnection of many networks**.

---

# 🧠 Quick Revision

```
NETWORK
= Devices communicating and sharing resources

LAN
= Local Area Network

WAN
= Wide Area Network

SWITCH
= Primarily forwards Ethernet frames using MAC addresses

ROUTER
= Connects IP networks and forwards packets

FIREWALL
= Controls traffic using security policies

ACCESS POINT
= Provides wireless network connectivity

UNICAST
= 1 → 1

BROADCAST
= 1 → ALL

MULTICAST
= 1 → GROUP
```

---

# 🎯 Assignment — Test Yourself

Try answering these questions **without looking back at the chapter**.

### Beginner

1. What is a computer network?
2. Why do we need networking?
3. Name five devices that can participate in a network.
4. What is the purpose of a switch?
5. What is the purpose of a router?
6. What is the purpose of a firewall?
7. What does an Access Point do?

### Understanding

8. What is the difference between a LAN and WAN?
9. What is PAN?
10. What is MAN?
11. What is unicast?
12. What is broadcast?
13. What is multicast?
14. What is the difference between a frame and a packet?
15. Why does a router connect different networks?

### Think Like a Network Engineer

16. PC1 wants to communicate with another PC on the same LAN. Which device is primarily responsible for forwarding the Ethernet frame?
    
17. PC1 wants to communicate with a server on another IP network. Which device is responsible for forwarding the packet toward the destination network?
    
18. A user can access the local gateway but cannot access the Internet. What devices and network components would you investigate?
    
19. A user can ping a server but cannot access its web application. What could be different between ICMP traffic and application traffic?
    
20. A user says, "The Internet is down." What questions would you ask before troubleshooting?
    

---

# 📝 What You Should Understand Before Moving On

Before starting the next chapter, you should be able to explain these concepts **in your own words**:

- What a network is
- Why networks are needed
- What an end device is
- What a switch does
- What a router does
- What a firewall does
- What an Access Point does
- LAN vs WAN
- Unicast vs Broadcast vs Multicast
- Frame vs Packet
- Basic network communication flow

If you can explain these concepts without memorizing the definitions, you're ready for the next chapter.

---

# 🚀 Next Chapter

## Chapter 2 — OSI Model

In the next chapter, we will break networking communication into seven layers and understand what happens to data as it moves through the network.
```text
Chapter 1
What is a Network?
       ↓
Chapter 2
OSI Model
       ↓
Chapter 3
TCP/IP Model
       ↓
Chapter 4
Encapsulation & Decapsulation
       ↓
```
# 🌐 Keep Wandering
> **Understand the layer. Understand the problem. Understand the network.**

**Your first packet is only the beginning.**
