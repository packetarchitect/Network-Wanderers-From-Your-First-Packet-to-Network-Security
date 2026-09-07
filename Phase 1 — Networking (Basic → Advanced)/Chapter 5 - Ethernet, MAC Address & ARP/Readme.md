# 📘 Module 1 – Networking Fundamentals

# Chapter 5 – Ethernet, MAC Address & ARP (Complete Deep Dive)

---

## 🎯 Chapter Objective

By the end of this chapter, you should be able to explain how Ethernet works at Layer 2, how switches learn MAC addresses, how ARP resolves IPv4 addresses to MAC addresses, how ARP and MAC tables differ, and how these concepts are used in real enterprise troubleshooting and Layer 2 security.

---

# 5.1 What is Ethernet?

Ethernet is the most widely used **Layer 2 (Data Link Layer)** technology for communication in Local Area Networks (LANs).

It defines:

- How devices send and receive data over a LAN
- Ethernet frame format
- MAC addressing
- Error detection
- Media access

**IEEE Standard:** **802.3**

### Real-Life Example

```text
PC1 ─┐
PC2 ─┤
PC3 ─┤
     │
  Switch
     │
 Firewall
```

Communication between devices on the LAN uses **Ethernet frames**.

### Big Picture

```text
Application Data
       ↓
   TCP / UDP
       ↓
      IP
       ↓
 Ethernet Frame
       ↓
 MAC Address
       ↓
     Switch
       ↓
 Physical Network
```

---

# 5.2 What is a MAC Address?

A **MAC (Media Access Control) Address** is a Layer 2 address associated with a network interface.

It identifies a network interface on the local network.

Example:

```text
00:1A:2B:3C:4D:5E
```

or:

```text
00-1A-2B-3C-4D-5E
```

> **Important:** A MAC address is commonly called a "physical" or "hardware" address, but modern systems can use software-configured or randomized MAC addresses. Think of it primarily as a **Layer 2 interface identifier**.

## MAC Address Length

A traditional Ethernet MAC address is:

- **48 bits**
- **6 bytes**
- **12 hexadecimal digits**

```text
00:50:56:AB:CD:EF
```

Each hexadecimal digit represents 4 bits:

```text
12 × 4 = 48 bits
```

---

# 5.3 MAC Address Structure

Example:

```text
00:1A:2B:3C:4D:5E
```

Traditionally, the first 24 bits represent the **OUI (Organizationally Unique Identifier)** and the remaining portion identifies the interface.

| Portion | Meaning |
|---|---|
| `00:1A:2B` | OUI / organizational identifier |
| `3C:4D:5E` | Remaining interface identifier |

## OUI

The OUI is assigned through IEEE registration and identifies an organization/manufacturer allocation.

Examples include:

- Cisco
- Intel
- Dell
- HP
- Apple

> **Interview Point:** Do not assume the OUI always proves the physical manufacturer. Virtual interfaces, locally administered addresses, and MAC randomization can affect how MAC addresses are used.

---

# 5.4 Types of MAC Addresses

## 1. Unicast

**One sender → One receiver**

```text
PC1 ─────────→ PC2
```

---

## 2. Broadcast

Broadcast MAC:

```text
FF:FF:FF:FF:FF:FF
```

A Layer 2 broadcast is delivered throughout the relevant Layer 2 broadcast domain/VLAN.

Examples:

- ARP Request
- DHCP Discover

```text
             ┌── PC2
             │
PC1 ─ Switch ├── PC3
             │
             └── PC4
```

---

## 3. Multicast

**One sender → Selected receivers**

Examples:

- IPTV
- Video conferencing
- Routing protocol traffic
- Other multicast applications

---

# 5.5 Ethernet Frame Structure

A simplified Ethernet frame:

```text
+----------+-------------+------------+----------+----------------+------+
| Preamble | Destination | Source MAC | EtherType| Payload        | FCS  |
+----------+-------------+------------+----------+----------------+------+
| 7 bytes  |   6 bytes   |  6 bytes   | 2 bytes  | 46-1500 bytes  | 4 B  |
+----------+-------------+------------+----------+----------------+------+
```

> The preamble is commonly discussed separately from the Ethernet frame itself at the PHY/MAC boundary. For practical troubleshooting, remember that it provides synchronization.

## Fields Explained

### 1. Preamble

**7 bytes**

Purpose:

- Synchronizes sender and receiver
- Helps the receiver prepare for the incoming frame

### 2. Destination MAC

**6 bytes**

Identifies the intended Layer 2 destination.

### 3. Source MAC

**6 bytes**

Identifies the transmitting interface.

### 4. EtherType

**2 bytes**

Identifies the protocol carried in the payload.

| EtherType | Protocol |
|---|---|
| `0x0800` | IPv4 |
| `0x86DD` | IPv6 |
| `0x0806` | ARP |
| `0x8100` | 802.1Q VLAN tagging |

### 5. Payload

Carries higher-layer information such as:

- IPv4 packet
- IPv6 packet
- ARP message

### 6. FCS

**FCS = Frame Check Sequence**

Used for error detection using a CRC mechanism.

```text
Sender
  ↓
Calculate CRC
  ↓
Put result in FCS
  ↓
Transmit
  ↓
Receiver checks
  ↓
Mismatch?
 ├─ No  → Continue
 └─ Yes → Frame considered corrupted
```

FCS provides error detection, **not encryption**.

---

# 5.6 Ethernet Frame Size and MTU

A common Ethernet network uses an MTU of:

```text
1500 bytes
```

This normally refers to the maximum IP packet size carried as the Ethernet payload on a standard Ethernet interface.

MTU problems can contribute to:

- Fragmentation
- Packet drops
- Path MTU Discovery problems
- Applications working inconsistently

This becomes especially important with:

- VPNs
- GRE/IPsec tunnels
- WAN links
- Firewalls
- Cloud networking

---

# 5.7 How a Switch Learns MAC Addresses

A Layer 2 switch builds a **MAC address table** by examining the **source MAC address** of incoming Ethernet frames.

Example:

```text
PC1
MAC = AA-AA-AA-AA-AA-AA
       │
       │ Gi0/1
       ▼
    Switch
```

The switch learns:

| MAC Address | Port |
|---|---|
| AA-AA-AA-AA-AA-AA | Gi0/1 |

Later:

```text
PC2
MAC = BB-BB-BB-BB-BB-BB
       │
       │ Gi0/2
       ▼
    Switch
```

The table becomes:

| MAC Address | Port |
|---|---|
| AA-AA-AA-AA-AA-AA | Gi0/1 |
| BB-BB-BB-BB-BB-BB | Gi0/2 |

---

# 5.8 MAC Address Table / CAM Table

A switch's forwarding database may be called:

- MAC address table
- CAM table
- Forwarding table

Example:

| VLAN | MAC Address | Port |
|---:|---|---|
| 10 | AA-AA-AA-AA-AA-AA | Gi0/1 |
| 10 | BB-BB-BB-BB-BB-BB | Gi0/2 |

Cisco command:

```text
show mac address-table
```

Remember:

```text
MAC → Port / VLAN
```

---

# 5.9 How a Switch Forwards a Frame

Suppose PC1 sends a frame to PC2.

```text
PC1
MAC = AA-AA-AA-AA-AA-AA
       │
       ▼
    Switch
       │
       ▼
PC2
MAC = BB-BB-BB-BB-BB-BB
```

### Step 1 – Learn Source

```text
AA-AA-AA-AA-AA-AA → Gi0/1
```

### Step 2 – Look Up Destination

If:

```text
BB-BB-BB-BB-BB-BB → Gi0/2
```

exists, the switch forwards the frame toward Gi0/2.

### Key Logic

```text
Source MAC
   ↓
LEARN

Destination MAC
   ↓
LOOK UP

Known?
 ├─ YES → Forward specific port
 └─ NO  → Flood within VLAN
```

---

# 5.10 Unknown Unicast

If the destination MAC is unknown, the switch floods the frame within the relevant VLAN, except the incoming port.

```text
             ┌── PC2
             │
PC1 ─ Switch ├── PC3
             │
             └── PC4
```

When PC3 replies, the switch can learn PC3's source MAC and associate it with the incoming port.

### Interview Point

```text
Known Unicast   → Specific port
Unknown Unicast → Flood within VLAN
Broadcast       → Flood within VLAN
```

---

# 5.11 MAC Address Aging

Learned MAC entries are normally temporary.

Conceptually:

```text
MAC learned
    ↓
No traffic for aging period
    ↓
Entry ages out
    ↓
Future traffic may require relearning
```

This allows the switch to adapt when a device moves to another port.

---

# 5.12 What is ARP?

**ARP = Address Resolution Protocol**

ARP is used in IPv4 networks to resolve:

```text
IPv4 Address
     ↓
MAC Address
```

Example:

```text
192.168.10.1
      ↓
BB-BB-BB-BB-BB-BB
```

---

# 5.13 Why is ARP Needed?

Suppose:

```text
PC
IP  = 192.168.10.50
MAC = AA-AA-AA-AA-AA-AA
```

wants to communicate with:

```text
Gateway
IP  = 192.168.10.1
MAC = ?
```

The PC knows the destination IP, but local Ethernet delivery requires a destination MAC.

ARP resolves the mapping.

---

# 5.14 ARP Process – Step by Step

## Step 1 – Check ARP Cache

The PC checks:

```text
192.168.10.1 → BB-BB-BB-BB-BB-BB
```

If present:

```text
Use MAC
  ↓
Build Ethernet frame
  ↓
Send
```

If absent, send an ARP Request.

## Step 2 – ARP Request

```text
Who has 192.168.10.1?
Tell 192.168.10.50
```

Destination MAC:

```text
FF:FF:FF:FF:FF:FF
```

The request is delivered throughout the local Layer 2 broadcast domain.

## Step 3 – ARP Reply

The device owning the IP replies:

```text
192.168.10.1
     ↓
BB-BB-BB-BB-BB-BB
```

The reply is normally unicast.

## Step 4 – Update ARP Cache

| IP Address | MAC Address |
|---|---|
| 192.168.10.1 | BB-BB-BB-BB-BB-BB |

---

# 5.15 Same Subnet vs Remote Network

This is one of the most important interview concepts.

### Destination on Same Subnet

Example:

```text
PC = 192.168.10.50
Dest = 192.168.10.20
```

The PC ARPs for the destination host:

```text
PC
 ↓ ARP
192.168.10.20's MAC
 ↓
Ethernet frame
```

### Destination on Different Network

Example:

```text
PC = 192.168.10.50
Dest = 8.8.8.8
```

The PC does **not** ARP for 8.8.8.8.

It ARPs for the default gateway:

```text
8.8.8.8
   ↓
Remote network
   ↓
Default Gateway
   ↓
Gateway MAC
```

Then:

```text
Destination IP = 8.8.8.8
Destination MAC = Gateway MAC
```

---

# 5.16 ARP Table

Windows:

```text
arp -a
```

Cisco IOS:

```text
show ip arp
```

FortiGate:

```text
get system arp
```

Example:

| IP Address | MAC Address |
|---|---|
| 192.168.10.1 | BB-BB-BB-BB-BB-BB |
| 192.168.10.20 | CC-CC-CC-CC-CC-CC |

---

# 5.17 MAC Table vs ARP Table

| MAC Address Table | ARP Table |
|---|---|
| Layer 2 forwarding information | IPv4 IP-to-MAC information |
| Usually maintained by switches | Maintained by hosts, routers, firewalls, etc. |
| MAC → Port/VLAN | IPv4 → MAC |
| Used for Ethernet frame forwarding | Used to determine Layer 2 destination for an IPv4 next hop |

### Memory

```text
MAC Table:
"Where is this MAC?"

ARP Table:
"What MAC corresponds to this IPv4 address?"
```

---

# 5.18 ARP Request vs ARP Reply

| ARP Request | ARP Reply |
|---|---|
| Usually broadcast | Usually unicast |
| "Who has this IP?" | "This IP is at this MAC." |
| Discovers mapping | Returns mapping |

---

# 5.19 Gratuitous ARP (GARP)

A **Gratuitous ARP** is an unsolicited ARP announcement commonly used to advertise a host's own IPv4-to-MAC mapping.

Uses:

- Detect duplicate IP addresses
- Update ARP caches
- High Availability failover
- Virtual IP movement

### HA Example

```text
Firewall A
Gateway IP = 192.168.10.1
       ↓
      FAILS
       ↓
Firewall B becomes active
       ↓
Sends GARP
       ↓
192.168.10.1 → Firewall B MAC
```

This helps neighboring devices update their ARP information.

---

# 5.20 Proxy ARP

With **Proxy ARP**, a router or Layer 3 device answers an ARP request on behalf of another device.

```text
Host A
  │
  │ ARP
  ▼
Router
  │
  │ answers on behalf of Host B
  ▼
Host B
```

It can be useful in particular or legacy designs, but should not be enabled unnecessarily.

---

# 5.21 ARP Spoofing / ARP Poisoning

ARP has no built-in authentication.

An attacker can send forged ARP information.

Normal:

```text
Gateway IP
    ↓
Real Gateway MAC
```

Poisoned:

```text
Gateway IP
    ↓
Attacker MAC
```

Potential consequences:

- Man-in-the-Middle (MITM)
- Credential theft
- Session interception
- Traffic manipulation
- Denial of service

---

# 5.22 Protection Against ARP Spoofing

## DHCP Snooping

Can build a trusted binding database such as:

```text
IP ↔ MAC ↔ VLAN ↔ Port
```

This information can be used by security mechanisms.

## Dynamic ARP Inspection (DAI)

DAI validates ARP packets against trusted bindings.

```text
ARP Packet
    ↓
DAI Validation
    ↓
Matches trusted binding?
   ├─ YES → Allow
   └─ NO  → Drop / handle according to configuration
```

## Static ARP / Static Bindings

Can be appropriate for selected critical systems depending on the environment.

---

# 5.23 CAM Table Flooding Attack

A switch has finite forwarding-table resources.

An attacker may send frames with many different source MAC addresses:

```text
Attacker
   ↓
MAC 1
MAC 2
MAC 3
MAC 4
...
Thousands of MACs
   ↓
Switch MAC/CAM table
   ↓
Resources exhausted
```

This is known as **MAC/CAM table flooding**.

Potentially, unknown destinations may then be flooded more broadly.

> Exact behavior depends on switch hardware, software, configuration, VLAN design, and security controls.

---

# 5.24 Protection Against CAM Table Flooding

Common controls:

- Port Security
- MAC address limits
- Storm Control
- Monitoring abnormal MAC learning
- Appropriate switch security configuration

Example:

```text
Gi0/1
Allowed MAC count = 1
```

If unexpected MAC addresses appear, the configured violation action can be triggered.

---

# 5.25 Complete Ethernet + ARP Packet Flow

### Laptop

```text
IP  = 192.168.10.50
MAC = AA-AA-AA-AA-AA-AA
```

### Gateway

```text
IP  = 192.168.10.1
MAC = BB-BB-BB-BB-BB-BB
```

### Destination

```text
8.8.8.8
```

Because the destination is remote, the laptop needs the gateway's MAC.

```text
1. Laptop checks ARP cache.
2. Gateway MAC is not present.
3. Laptop creates an ARP Request.
4. Destination MAC = FF:FF:FF:FF:FF:FF.
5. Switch floods the broadcast within the VLAN.
6. Gateway receives the ARP Request.
7. Gateway sends an ARP Reply.
8. Laptop stores 192.168.10.1 → BB-BB-BB-BB-BB-BB.
9. Laptop creates an Ethernet frame.
10. Destination MAC = BB-BB-BB-BB-BB-BB.
11. Source MAC = AA-AA-AA-AA-AA-AA.
12. Payload contains the IP packet:
    Source IP = 192.168.10.50
    Destination IP = 8.8.8.8
13. Switch looks up the gateway MAC.
14. Switch forwards the frame toward the gateway.
15. Router/firewall receives the frame.
16. Layer 2 information is processed.
17. The Layer 3 device routes the IP packet.
18. A new Layer 2 frame is created on the outgoing interface.
```

### Critical Concept

```text
MAC addresses
→ change hop-by-hop

IP source/destination
→ generally remain end-to-end
```

---

# 5.26 Real Enterprise Troubleshooting

### Scenario

User:

> "I cannot access the Internet."

Do not immediately assume the firewall is broken.

Troubleshoot from the endpoint outward.

## Step 1 – Check IP Configuration

Windows:

```text
ipconfig /all
```

Verify:

- IP address
- Subnet mask
- Default gateway
- DNS servers

## Step 2 – Test Local Stack

```text
ping 127.0.0.1
```

## Step 3 – Test Default Gateway

```text
ping 192.168.10.1
```

If it fails, investigate:

- VLAN
- Switch port
- Cabling/Wi-Fi
- IP configuration
- ARP
- Gateway availability
- Interface state

## Step 4 – Check ARP

```text
arp -a
```

Look for:

```text
192.168.10.1 → BB-BB-BB-BB-BB-BB
```

If missing, investigate:

- Gateway availability
- VLAN mismatch
- Layer 2 connectivity
- Interface state
- ARP behavior

## Step 5 – Check Routing

Windows:

```text
tracert 8.8.8.8
```

Linux:

```text
traceroute 8.8.8.8
```

Investigate:

- Default route
- Upstream routing
- Firewall policy
- NAT
- WAN connectivity

## Step 6 – Check Firewall

Conceptually:

```text
Ingress Interface
       ↓
Policy Match
       ↓
NAT
       ↓
Routing
       ↓
Egress Interface
```

For FortiGate environments, this can include checking ARP, routing, policy matching, session information, and packet-flow/debug tools.

---

# 5.27 Troubleshooting Flow

```text
             User cannot access Internet
                       │
                       ▼
               Check IP Configuration
                       │
                       ▼
              Is Default Gateway Correct?
                  │              │
                 NO             YES
                  │              │
             Fix IP config       ▼
                            Ping Gateway
                               │
                      ┌────────┴────────┐
                     FAIL              PASS
                      │                  │
                      ▼                  ▼
                Check L2/ARP       Check Routing
                VLAN/Port          Firewall/NAT
                                         │
                                         ▼
                                  Check DNS/Application
```

### Key Principle

```text
Don't jump directly to the firewall.
Start at the endpoint and move hop-by-hop.
```

---

# 5.28 Wireshark Perspective

Useful display filter:

```text
arp
```

Example ARP Request:

```text
Ethernet
Destination: ff:ff:ff:ff:ff:ff
Source:      aa:aa:aa:aa:aa:aa

ARP
Who has 192.168.10.1?
```

Example ARP Reply:

```text
Ethernet
Destination: aa:aa:aa:aa:aa:aa
Source:      bb:bb:bb:bb:bb:bb

ARP
192.168.10.1 is at bb:bb:bb:bb:bb:bb
```

To focus on a specific address:

```text
arp && ip.addr == 192.168.10.1
```

---

# 5.29 Common Interview Questions

### Q1. Why is ARP needed?

ARP is used in IPv4 networks to resolve an IP address to the Layer 2 MAC address needed for local Ethernet delivery.

### Q2. Does ARP work across routers?

No. ARP requests are Layer 2 broadcasts and routers normally do not forward them between different IP networks.

For a remote destination, the host resolves the MAC address of its local next hop, normally the default gateway.

### Q3. What is the difference between an ARP Request and an ARP Reply?

| ARP Request | ARP Reply |
|---|---|
| Usually broadcast | Usually unicast |
| "Who has this IP?" | "This IP is at this MAC." |
| Discovers mapping | Returns mapping |

### Q4. How does a switch learn MAC addresses?

By examining the source MAC address of incoming Ethernet frames and associating it with the ingress port and VLAN.

### Q5. What happens if the destination MAC is unknown?

The switch floods the frame within the relevant VLAN, excluding the incoming port.

### Q6. What is the difference between a MAC table and an ARP table?

```text
MAC Table:
MAC → Port/VLAN

ARP Table:
IPv4 → MAC
```

### Q7. When does a host ARP for the destination and when does it ARP for the gateway?

- Same subnet → ARP for destination host.
- Remote subnet → ARP for local next hop/default gateway.

### Q8. What is Gratuitous ARP?

An unsolicited ARP announcement commonly used for ARP-cache updates, duplicate-IP detection, and HA/virtual-IP failover.

### Q9. What is ARP spoofing?

An attack in which forged ARP information causes a host to associate an IP address, such as the gateway, with an attacker's MAC address.

### Q10. How can ARP spoofing be mitigated?

Common controls include:

- DHCP Snooping
- Dynamic ARP Inspection
- Static bindings where appropriate
- Network segmentation
- Monitoring

### Q11. What is CAM table flooding?

An attack that attempts to exhaust switch MAC forwarding-table resources by generating traffic with many source MAC addresses.

### Q12. How can CAM table flooding be mitigated?

Common controls include:

- Port Security
- MAC address limits
- Storm Control
- Monitoring

---

# 🧠 Memory Trick

## "MAC Finds the Port, ARP Finds the MAC."

```text
IP Address
    │
    │ ARP
    ▼
MAC Address
    │
    │ Switch MAC Table
    ▼
Switch Port
```

And:

```text
ARP Table → "What MAC?"
MAC Table → "What Port?"
```

---

# ⭐ Gold Interview Tip

If an interviewer asks:

> **"A PC wants to reach 8.8.8.8. What MAC address will it use?"**

Do not say:

> "It will ARP for 8.8.8.8."

Say:

> **"Because 8.8.8.8 is outside the local subnet, the PC sends the Ethernet frame to the MAC address of its default gateway. It ARPs for the gateway's IP address, not for the remote destination."**

This demonstrates that you understand **Layer 2 local delivery versus Layer 3 routing**.

---

# ⚡ Quick Revision Notes

```text
Ethernet
→ Layer 2 LAN technology
→ IEEE 802.3

MAC
→ Layer 2 interface address
→ Usually 48 bits / 6 bytes

Ethernet Frame
→ Destination MAC
→ Source MAC
→ EtherType
→ Payload
→ FCS

Switch
→ Learns source MAC
→ Stores MAC → Port/VLAN
→ Forwards known unicast
→ Floods unknown unicast/broadcast within VLAN

ARP
→ IPv4 → MAC resolution
→ Request usually broadcast
→ Reply usually unicast

Same subnet
→ ARP for destination host

Remote subnet
→ ARP for default gateway / next hop

ARP Table
→ IPv4 → MAC

MAC Table
→ MAC → Port/VLAN

GARP
→ HA / cache update / duplicate-IP detection

Proxy ARP
→ Device answers ARP on behalf of another

ARP Spoofing
→ Fake IP → MAC mapping
→ MITM risk

DAI
→ Validates ARP against trusted bindings

DHCP Snooping
→ Builds trusted IP/MAC/port bindings

CAM Flooding
→ Attempts to exhaust MAC table

Port Security
→ Limits/controls MAC addresses on ports
```

---

# 🎯 Assignment — Test Yourself

Try answering these **without looking at the chapter**.

## Basic

1. What is Ethernet?
2. What IEEE standard defines Ethernet?
3. What is a MAC address?
4. How many bits are in a traditional Ethernet MAC address?
5. What is a unicast MAC address?
6. What is the Ethernet broadcast MAC address?

## Intermediate

7. What is an Ethernet frame?
8. What is the purpose of the EtherType field?
9. What is FCS?
10. How does a switch learn a MAC address?
11. What is a CAM table?
12. What happens when a destination MAC is unknown?
13. What is ARP?
14. Why is ARP required for IPv4 Ethernet communication?
15. What is the difference between an ARP table and a MAC table?

## Advanced

16. A PC wants to reach `8.8.8.8`. Which MAC address does it put in the Ethernet frame?
17. Why doesn't the PC ARP for `8.8.8.8`?
18. What happens to MAC addresses when a packet crosses a router?
19. What is Gratuitous ARP?
20. What is Proxy ARP?
21. How does ARP spoofing work?
22. What is Dynamic ARP Inspection?
23. Why is DHCP Snooping useful with DAI?
24. What is CAM table flooding?
25. How would you troubleshoot a PC that cannot ping its default gateway?

## 🎯 Challenge Question

A user says:

> "My PC has a valid IP address, but I cannot access the Internet. The default gateway does not appear in `arp -a`."

Walk through your troubleshooting process step by step.

```text
Endpoint
   ↓
IP Configuration
   ↓
ARP
   ↓
VLAN / Switch Port
   ↓
Gateway
   ↓
Firewall
   ↓
Routing / NAT
```

---

# 🧪 Practical Assignment — Lab Practice

## Lab 1 – View Your MAC Address

### Windows

```text
ipconfig /all
```

or:

```text
getmac
```

### Linux

```text
ip link
```

Identify:

- Interface name
- MAC address
- IPv4 address

---

## Lab 2 – View the ARP Cache

### Windows

```text
arp -a
```

### Linux

```text
ip neigh
```

Identify:

- Gateway IP
- Gateway MAC
- Neighbor state

---

## Lab 3 – Observe ARP with Wireshark

1. Start a capture.
2. Clear/reduce the relevant ARP cache entry where appropriate.
3. Ping the default gateway.
4. Apply:

```text
arp
```

5. Identify:
   - ARP Request
   - ARP Reply
   - Source MAC
   - Destination MAC
   - Sender IP
   - Target IP

---

## Lab 4 – Cisco Switch MAC Table

On a Cisco switch:

```text
show mac address-table
```

Identify:

- VLAN
- MAC address
- Port
- Dynamic/static status

Then connect a host and observe how the MAC table changes.

---

## Lab 5 – FortiGate ARP Table

On FortiGate:

```text
get system arp
```

Identify:

- IP address
- MAC address
- Interface

Then compare with the endpoint:

```text
arp -a
```

Ask yourself:

> Why does the endpoint see the gateway MAC, while the firewall sees the endpoint MAC?

---

## Lab 6 – Complete Packet Journey

Build:

```text
PC
 │
 │ Ethernet
 ▼
Switch
 │
 │ Ethernet
 ▼
Firewall/Router
 │
 │ Routed traffic
 ▼
Internet
```

Document:

1. PC IP
2. PC MAC
3. Gateway IP
4. Gateway MAC
5. Switch port
6. MAC table entry
7. ARP table entry
8. Firewall interface
9. Next hop

Then explain the packet flow from the PC to the Internet.

---

# 🏆 Final Interview Scenario

A user reports:

> "I have an IP address, but I cannot access the Internet."

You check:

```text
IP Address:
192.168.10.50

Default Gateway:
192.168.10.1
```

But:

```text
arp -a
```

does not show:

```text
192.168.10.1
```

Investigate in this order:

```text
1. Is the IP configuration correct?
             ↓
2. Is the interface connected?
             ↓
3. Is the correct VLAN assigned?
             ↓
4. Is the switch port up?
             ↓
5. Is the gateway interface up?
             ↓
6. Is ARP Request leaving the PC?
             ↓
7. Is ARP Request reaching the gateway?
             ↓
8. Is ARP Reply returning?
             ↓
9. Does the switch learn the PC MAC?
             ↓
10. Does the firewall/router see the client?
```

This is the beginning of **real network troubleshooting**.

---

# 🎤 Interview Answer — 3–4 Minutes

> **"Ethernet is the IEEE 802.3 Layer 2 technology commonly used in LANs. Ethernet communication uses frames containing source and destination MAC addresses, an EtherType field, payload, and an FCS for error detection. Switches learn MAC addresses by examining the source MAC of incoming frames and associating that MAC with an ingress port and VLAN. They then use the MAC address table to forward known unicast traffic and flood unknown destinations within the relevant VLAN.
>
> In IPv4 networks, ARP is used to resolve an IP address to a MAC address for local Layer 2 delivery. The host first checks its ARP cache. If the mapping is missing, it sends an ARP Request as a Layer 2 broadcast. The device owning the IP responds, normally with a unicast ARP Reply, and the host stores the mapping in its ARP cache.
>
> An important concept is that a host does not ARP for a remote Internet destination. If the destination is outside the local subnet, the host ARPs for its default gateway or local next hop and sends the Ethernet frame to that MAC address. When the packet crosses a router, the Layer 2 MAC addresses change for the next hop while the Layer 3 destination remains the remote destination.
>
> From a security perspective, ARP can be abused through ARP spoofing, while switches can be targeted with MAC or CAM table flooding. Enterprise controls such as DHCP Snooping, Dynamic ARP Inspection, Port Security, MAC limits, and appropriate monitoring can reduce these risks.
>
> From a troubleshooting perspective, I would use ARP and MAC tables to determine whether the problem is at the endpoint, Layer 2 network, gateway, firewall, routing, or NAT stage. This allows me to troubleshoot systematically instead of immediately assuming that the firewall is the problem."**

---

# 📚 Chapter Summary

By the end of this chapter, you should confidently understand:

- ✅ Ethernet and IEEE 802.3
- ✅ MAC addresses
- ✅ MAC address types
- ✅ OUI
- ✅ Ethernet frame structure
- ✅ EtherType
- ✅ FCS and CRC
- ✅ MTU
- ✅ Switch MAC learning
- ✅ MAC/CAM address table
- ✅ Unknown unicast flooding
- ✅ MAC aging
- ✅ ARP
- ✅ ARP Request and Reply
- ✅ Same-subnet ARP
- ✅ Remote-network gateway ARP
- ✅ ARP cache/table
- ✅ MAC table vs ARP table
- ✅ Gratuitous ARP
- ✅ Proxy ARP
- ✅ ARP spoofing
- ✅ DHCP Snooping
- ✅ Dynamic ARP Inspection
- ✅ CAM table flooding
- ✅ Port Security
- ✅ Ethernet + ARP packet flow
- ✅ Wireshark ARP troubleshooting
- ✅ Enterprise Layer 2 troubleshooting

---

# 🚀 Next Chapter

## Chapter 6 – Switching Fundamentals, VLANs, Trunking & STP

Next we move deeper into Layer 2 switching:

```text
Ethernet
    ↓
MAC Address
    ↓
MAC Table
    ↓
Switch
    ↓
VLAN
    ↓
Trunk
    ↓
STP
```

We will cover:

- What a switch does internally
- Access ports
- Trunk ports
- VLANs
- 802.1Q tagging
- Native VLAN
- Inter-VLAN communication
- Router-on-a-Stick
- Layer 3 switching
- Broadcast domains
- Collision domains
- STP
- Root Bridge
- STP port states
- VLAN/STP troubleshooting
- Real enterprise switch/firewall scenarios

This becomes the foundation for understanding **enterprise LAN architecture**.

---

# 🌐 Keep Wandering

Networking becomes much easier when you stop memorizing isolated terms and start following the **packet's journey**.

Keep asking:

```text
What is the destination IP?
        ↓
Is it local or remote?
        ↓
Which next hop is required?
        ↓
What MAC address is needed?
        ↓
Which switch port has that MAC?
        ↓
Does the frame reach the gateway?
        ↓
Does the router/firewall route it?
        ↓
What happens at the next hop?
```

That mindset takes you from:

```text
Networking Fundamentals
        ↓
Switching
        ↓
Routing
        ↓
Firewalls
        ↓
Packet Capture
        ↓
Security
        ↓
Advanced Troubleshooting
```

**Don't just learn networking. Learn to follow the packet. 🚀**
