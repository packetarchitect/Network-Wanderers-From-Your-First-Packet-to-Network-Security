# 📘 Module 3 – Routing

# Chapter 12 – Routing Fundamentals (Basic to Advanced)

---

# 📚 What This Chapter Covers

- What is Routing?
- Why Routing is Required
- Router vs Switch
- Layer 2 vs Layer 3 Communication
- How Routers Work
- Routing Table
- Routing Decision Process
- Longest Prefix Match (LPM)
- Administrative Distance (Introduction)
- Routing Metrics (Introduction)
- Default Route (0.0.0.0/0)
- Connected Routes
- Static Routes (Introduction)
- Dynamic Routes (Introduction)
- Recursive Route Lookup
- Packet Flow Through a Router
- Inter-VLAN Routing
- Routing Between Sites
- Enterprise Routing Design
- Hardware vs Software Forwarding
- CEF (Cisco Express Forwarding) Basics
- Real-World Enterprise Examples
- Troubleshooting Routing Issues
- Common Interview Questions
- Hands-on Labs
- Key Takeaways
- Quick Revision Notes

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain what routing is and why it is required.
- Understand how routers forward packets.
- Read and interpret a routing table.
- Explain Longest Prefix Match (LPM).
- Differentiate between connected, static, and dynamic routes.
- Describe packet flow through a router.
- Troubleshoot common routing problems.
- Answer routing interview questions confidently.

---

# 1. What is Routing?

**Routing** is the process of forwarding packets from one network to another using the best available path.

A **router** examines the destination IP address of a packet and decides where to send it next.

### Interview Definition

> **Routing is the Layer 3 process of selecting the best path for forwarding IP packets between different networks based on the routing table.**

---

# 2. Why Do We Need Routing?

Imagine a company with three departments:

```text
HR VLAN 10
192.168.10.0/24

Finance VLAN 20
192.168.20.0/24

IT VLAN 30
192.168.30.0/24
```

Each VLAN is a separate network.

Can HR directly communicate with Finance?

❌ **No**

To communicate between different networks, a **Layer 3 device** is required.

```text
HR PC
192.168.10.10
      │
      ▼
Layer 3 Switch / Router / Firewall
      │
      ▼
Finance PC
192.168.20.20
```

This Layer 3 forwarding process is called **routing**.

## Real-Life Analogy

- **Street** = Local Network
- **City** = Different Network
- **Courier Hub** = Router

---

# 3. Router vs Switch

| Feature | Switch | Router |
|---|---|---|
| OSI Layer | Layer 2 | Layer 3 |
| Uses | MAC Address | IP Address |
| Table | CAM Table | Routing Table |
| Connects | Devices in the same network/VLAN | Different networks |
| Broadcasts | Forwards Layer 2 broadcasts within a VLAN | Does not forward Layer 2 broadcasts between interfaces |

---

# 4. How Does a Router Work?

Suppose a packet arrives:

```text
Source: 192.168.10.10
Destination: 172.16.5.20
```

The router:

1. Receives the Ethernet frame.
2. Processes the incoming Layer 2 encapsulation.
3. Reads the destination IP.
4. Looks up the destination in the routing table.
5. Selects the best matching route.
6. Determines the next hop or outgoing interface.
7. Builds a new Layer 2 frame.
8. Forwards the packet.

---

# 5. Packet Flow Through a Router

```text
PC1
192.168.10.10
      │
      ▼
Router
      │
      ▼
Server
172.16.5.20
```

### Step 1 – PC Checks Destination

PC1 compares:

- My network → `192.168.10.0/24`
- Destination → `172.16.5.20`

Different network, so the PC sends the packet to its **default gateway**.

### Step 2 – Router Receives Frame

```text
Ethernet Header
IP Packet
TCP Segment
Data
```

### Step 3 – Layer 2 Header Is Processed

The router processes the incoming Layer 2 encapsulation and examines the Layer 3 packet.

### Step 4 – Routing Table Lookup

| Network | Next Hop |
|---|---|
| 192.168.10.0/24 | Connected |
| 172.16.5.0/24 | Connected |
| 0.0.0.0/0 | ISP |

The router finds `172.16.5.0/24`.

### Step 5 – New Layer 2 Header

The router creates a new Ethernet frame:

- Source MAC = outgoing interface MAC
- Destination MAC = next hop or destination device MAC

The IP addresses normally remain unchanged unless NAT or another packet transformation occurs.

---

# 6. Routing Table

A routing table contains information such as:

- Destination Network
- Prefix
- Next Hop
- Outgoing Interface
- Metric
- Administrative Distance

Example:

```text
C 192.168.10.0/24 → Gi0/1
C 172.16.5.0/24 → Gi0/2
S 10.10.10.0/24 → 172.16.5.1
S* 0.0.0.0/0 → 203.0.113.1
```

Legend:

- **C** = Connected
- **S** = Static
- **S*** = Default Static Route

---

# 7. Connected Routes

When a router interface has an IP address and is operational, the router automatically installs a connected route for the directly connected subnet.

Example:

```text
Gi0/1
192.168.10.1/24
```

Routing table:

```text
C 192.168.10.0/24
```

No manual route is required for that directly connected network.

---

# 8. Default Route

A default route is used when **no more specific route exists**.

```text
0.0.0.0/0
```

Think:

> **"If I don't know where to send the packet, send it here."**

Typically:

```text
Router
   ↓
ISP
   ↓
Internet
```

---

# 9. Longest Prefix Match (LPM)

Suppose:

| Route | Prefix |
|---|---|
| 10.0.0.0/8 | General |
| 10.10.0.0/16 | More specific |
| 10.10.20.0/24 | Most specific |

Destination:

```text
10.10.20.15
```

The router chooses:

```text
10.10.20.0/24
```

because it has the **longest matching prefix**.

The most specific matching route wins.

---

# 10. Routing Decision Process

```text
Packet Arrives
      │
      ▼
Read Destination IP
      │
      ▼
Search Routing Table
      │
      ▼
Apply Longest Prefix Match
      │
      ▼
If Multiple Candidate Routes
      │
      ▼
Use Route-Selection Rules
      │
      ▼
Forward Packet
```

**Important distinction:**

- **LPM** identifies the most specific matching destination prefix.
- **Administrative Distance** helps select between competing route sources for the same destination prefix.
- **Metrics** compare paths within the relevant routing protocol.

---

# 11. Administrative Distance – Introduction

**Administrative Distance (AD)** is a Cisco route-selection concept used to judge the trustworthiness of routes learned from different routing sources.

A **lower AD is preferred**.

Example:

```text
Connected → AD 0
Static → AD 1
OSPF → AD 110
```

Detailed AD values and route-selection behavior will be covered later.

---

# 12. Routing Metrics – Introduction

A **metric** is a value used by a routing protocol to compare paths learned through that protocol.

Examples:

- RIP → Hop count
- OSPF → Cost
- EIGRP → Composite metric

Conceptually:

```text
Routing Protocol
      ↓
Multiple Paths
      ↓
Compare Metric
      ↓
Choose Preferred Path
```

---

# 13. Static Routes – Introduction

A **static route** is manually configured by an administrator.

Example:

```text
ip route 10.10.10.0 255.255.255.0 172.16.5.1
```

Advantages:

- Simple for small networks.
- Predictable.
- No routing protocol overhead.

Disadvantages:

- Manual configuration.
- Does not automatically adapt to topology changes.
- Difficult to maintain at large scale.

Static routing is covered in detail in Chapter 13.

---

# 14. Dynamic Routes – Introduction

Dynamic routing uses routing protocols to learn networks automatically.

Examples:

- OSPF
- EIGRP
- BGP
- RIP

Routers exchange routing information and can adapt when topology changes.

Dynamic routing will be covered in later chapters.

---

# 15. Recursive Route Lookup

A recursive route lookup occurs when a routing entry points to a **next-hop IP address**, and the router must perform another lookup to determine how to reach that next hop.

Example:

```text
Destination:
10.10.10.0/24

Next Hop:
172.16.5.1
```

The router must also know:

```text
How do I reach 172.16.5.1?
```

Conceptually:

```text
Destination
    ↓
Route Lookup
    ↓
Next-Hop IP
    ↓
Another Lookup
    ↓
Outgoing Interface
```

---

# 16. Inter-VLAN Routing

VLANs create separate Layer 2 broadcast domains.

```text
VLAN 10
192.168.10.0/24

VLAN 20
192.168.20.0/24
```

To communicate between them, a Layer 3 gateway is required.

Possible solutions:

- Router-on-a-Stick
- Layer 3 Switch / SVIs
- Firewall

```text
VLAN 10
   │
   ▼
Layer 3 Gateway
   │
   ▼
VLAN 20
```

---

# 17. Routing Between Sites

```text
Branch A
192.168.10.0/24
       │
       ▼
MPLS / VPN / WAN
       │
       ▼
Head Office
10.10.10.0/24
       │
       ▼
Data Center
172.16.0.0/16
```

Routing allows:

- Branch users to access centralized applications.
- Secure communication over WAN.
- Efficient forwarding across sites.

Possible technologies include static routing, OSPF, BGP, SD-WAN, MPLS-based designs, or other enterprise approaches.

---

# 18. Enterprise Routing Design

A typical enterprise design may look like:

```text
                 Internet
                    │
                 Edge Router
                    │
             Firewall / Security
                    │
               Core Layer
              /                 Distribution A   Distribution B
          /    \            /          Access Access      Access Access
```

Important goals:

- Redundancy.
- Predictable routing.
- Fast convergence.
- Security boundaries.
- Route summarization where appropriate.
- Clear default-route strategy.
- Easy troubleshooting.

---

# 19. Hardware vs Software Forwarding

Enterprise routers and firewalls often use dedicated hardware to accelerate forwarding.

## Software Forwarding

- CPU processes packets.
- Generally lower performance.
- Common in small devices or virtual routers.

## Hardware Forwarding

Uses specialized hardware such as ASICs or network processors.

Examples:

- Cisco ASICs
- Fortinet NP (Network Processor)
- Juniper Trio

Benefits:

- High throughput.
- Lower general-purpose CPU utilization.
- Better performance under heavy traffic.

---

# 20. Cisco Express Forwarding (CEF)

**CEF (Cisco Express Forwarding)** is Cisco's optimized packet-forwarding mechanism.

Conceptually:

```text
Routing Table
     ↓
FIB
     ↓
Forwarding Decision

Adjacency Information
     ↓
Layer 2 Rewrite
```

CEF provides:

- Faster packet forwarding.
- Reduced CPU overhead.
- High scalability.

---

# 21. Real-World Enterprise Example

A bank has:

```text
Branch A
192.168.10.0/24

       ↓
MPLS / VPN

       ↓

Head Office
10.10.10.0/24

       ↓

Data Center
172.16.0.0/16
```

Routing allows:

- Branch users to access centralized applications.
- Secure communication over WAN.
- Efficient forwarding between sites.
- Controlled paths toward the data center and Internet.

---

# 22. Troubleshooting Routing Issues

## Problem: Cannot Reach Another Network

Check:

1. Correct IP address.
2. Correct subnet mask.
3. Default gateway.
4. Interface status.
5. Routing table.
6. Next-hop reachability.
7. ACLs.
8. Firewall policy.
9. NAT, if applicable.
10. Return route.

### Useful Cisco Commands

```text
show ip route
show ip interface brief
ping <ip-address>
traceroute <ip-address>
```

### FortiGate Commands

```text
get router info routing-table all
execute ping <ip-address>
diagnose ip route list
```

### Troubleshooting Flow

```text
Source Host
    ↓
IP / Mask
    ↓
Default Gateway
    ↓
Local Gateway
    ↓
Routing Table
    ↓
Next Hop
    ↓
Outbound Interface
    ↓
Destination
    ↓
Return Route
    ↓
ACL / Firewall / NAT
```

---

# 23. Common Interview Questions

### Q1. What is routing?

Routing is the Layer 3 process of forwarding packets between different IP networks using the best available path.

### Q2. What is a routing table?

A routing table is a database maintained by a router that contains destination networks, next hops, outgoing interfaces, and route-selection information.

### Q3. What is Longest Prefix Match?

When multiple routes match a destination, the router selects the route with the most specific prefix, meaning the longest matching subnet prefix.

### Q4. Why do MAC addresses change but IP addresses usually remain the same?

MAC addresses are local to a Layer 2 segment. At each routed hop, a new Layer 2 frame is created with new MAC addresses, while IP addresses normally remain unchanged unless NAT or another transformation occurs.

### Q5. What is the purpose of the default route?

It provides a route for destinations that do not have a more specific matching entry.

### Q6. What is the difference between a routing table and a forwarding table?

The routing table represents learned and configured routes, while the forwarding table contains optimized information used to make packet-forwarding decisions.

### Q7. What is Administrative Distance?

Administrative Distance is a Cisco route-source preference value used to choose between routes to the same destination prefix learned from different sources. Lower AD is preferred.

### Q8. What is a routing metric?

A routing metric is a protocol-specific value used to compare paths learned by a routing protocol.

### Q9. What is a recursive route lookup?

It occurs when a route points to a next-hop IP address and the router must perform another lookup to determine how to reach that next hop.

### Q10. What is CEF?

CEF, or Cisco Express Forwarding, is Cisco's optimized forwarding mechanism that uses structures such as the FIB and adjacency information to forward packets efficiently.

### Q11. Can a Layer 2 switch perform routing?

A traditional Layer 2 switch cannot route between IP networks, but a Layer 3 switch can perform routing using Layer 3 interfaces and SVIs.

### Q12. What happens if there is no route to the destination?

If there is no matching route and no usable default route, the router cannot forward the packet toward that destination and normally drops it.

---

# 🎤 Interview Answer – Explain Routing in 3–5 Minutes

> **"Routing is the Layer 3 process of forwarding IP packets between different networks. A router receives a frame, examines the destination IP address, consults its routing information, selects the most specific matching route using Longest Prefix Match, determines the next hop or outgoing interface, creates a new Layer 2 frame, and forwards the packet.**
>
> **A routing table can contain connected, static, and dynamically learned routes. A connected route is automatically installed for a directly connected subnet when the interface is operational. A static route is manually configured, while dynamic routing protocols such as OSPF, EIGRP, RIP, or BGP can learn routes automatically. A default route, 0.0.0.0/0, is used when no more specific route exists.**
>
> **When multiple routing sources provide a route to the same destination prefix, Administrative Distance can be used to prefer the more trusted source. When a routing protocol has multiple candidate paths, its metric is used to compare those paths. Recursive lookup is another important concept where a next-hop IP must itself be resolved through another route lookup.**
>
> **In enterprise networks, routing is used for inter-VLAN communication, branch-to-head-office connectivity, data-center access, and Internet connectivity. Cisco routers can use CEF to optimize forwarding through structures such as the FIB and adjacency information.**
>
> **For troubleshooting, I would verify the host IP address, subnet mask, default gateway, interface status, routing table, next-hop reachability, return route, ACLs, firewall policies, and NAT where applicable. Commands such as show ip route, show ip interface brief, ping, and traceroute are useful on Cisco devices, while FortiGate provides commands such as get router info routing-table all and execute ping.**
>
> **In summary, routing connects different IP networks and determines where packets should go next. Understanding routing tables, Longest Prefix Match, default routes, connected and static routes, route selection, and packet flow provides the foundation for advanced technologies such as OSPF, BGP, VPNs, and enterprise firewall routing."**

---

# 🧠 Memory Trick

Remember:

```text
ROUTING = WHERE SHOULD THE PACKET GO?
```

### Basic Flow

```text
Destination IP
      ↓
Routing Table
      ↓
Longest Prefix Match
      ↓
Route Selection
      ↓
Next Hop / Interface
      ↓
New Layer 2 Frame
      ↓
Forward
```

### Route Types

```text
C → Connected
S → Static
D → Dynamic
* → Default
```

### LPM

```text
/8
 ↓
/16
 ↓
/24
```

**Longest matching prefix wins.**

### Default Route

```text
0.0.0.0/0
```

Think:

> **"If nothing more specific matches, use the default."**

---

# ⭐ Gold Interview Tip

If an interviewer asks:

> **"How does a router decide where to send a packet?"**

Answer in this order:

```text
Destination IP
      ↓
Routing Table
      ↓
Longest Prefix Match
      ↓
Route Selection
      ↓
Next Hop / Outgoing Interface
      ↓
Layer 2 Rewrite
      ↓
Forward
```

Then mention:

> **"If multiple routes to the same destination prefix come from different sources, Administrative Distance helps select the preferred source; metrics then help compare paths within the relevant routing protocol."**

This demonstrates that you understand the difference between **LPM, AD, and metrics**.

---

# 📝 Quick Revision Notes

| Topic | Remember |
|---|---|
| Routing | Layer 3 packet forwarding |
| Router | Connects different IP networks |
| Routing Table | Destination + Next Hop + Interface + selection information |
| Connected Route | Learned from directly connected operational interface |
| Static Route | Manually configured |
| Dynamic Route | Learned through routing protocols |
| Default Route | `0.0.0.0/0` |
| LPM | Most specific matching prefix |
| AD | Preference between different route sources |
| Metric | Compares paths within a routing protocol |
| Recursive Lookup | Resolves a next-hop IP through another lookup |
| Inter-VLAN Routing | Layer 3 forwarding between VLAN subnets |
| CEF | Cisco optimized forwarding |
| FIB | Forwarding Information Base |
| MAC | Changes across routed hops |
| IP | Normally remains end-to-end unless transformed |

---

# 🎯 Assignment – Test Yourself

## Theory

1. Define routing in your own words.
2. Explain why routing is required between VLANs.
3. Compare a Layer 2 switch with a router.
4. Explain the complete packet flow through a router.
5. Explain the purpose of a routing table.
6. Explain connected, static, and dynamic routes.
7. Explain the purpose of a default route.
8. Explain Longest Prefix Match with a practical example.
9. Explain Administrative Distance.
10. Explain routing metrics.
11. Explain recursive route lookup.
12. Explain inter-VLAN routing.
13. Explain the difference between a routing table and a forwarding table.
14. Explain CEF, FIB, and adjacency information.
15. Explain why MAC addresses change at routed hops.
16. Troubleshoot a host that can reach its gateway but cannot reach a remote subnet.
17. Give a 3–5 minute interview explanation of routing without looking at your notes.

---

# 🧪 Practical Assignment – Lab Practice

## Lab 1 – Basic Routing

Build this topology in EVE-NG, Packet Tracer, or GNS3:

```text
PC1
192.168.10.10/24
      │
      │
Gi0/1
Router
Gi0/2
      │
      │
Server
172.16.5.20/24
```

Router interfaces:

```text
Gi0/1 → 192.168.10.1/24
Gi0/2 → 172.16.5.1/24
```

Tasks:

1. Configure IP addresses.
2. Configure the correct default gateway on both hosts.
3. Verify interface status.
4. Check connected routes:
   ```text
   show ip route
   ```
5. Test:
   ```text
   ping 172.16.5.20
   ```
6. Run:
   ```text
   traceroute 172.16.5.20
   ```
7. Explain the packet flow hop by hop.

---

## Lab 2 – Longest Prefix Match

Create routes such as:

```text
10.0.0.0/8
10.10.0.0/16
10.10.20.0/24
```

Test destination:

```text
10.10.20.15
```

Tasks:

1. Determine which routes match.
2. Identify the winning route.
3. Explain why the `/24` route wins.
4. Repeat with other destination IPs.

---

## Lab 3 – Static Route + Recursive Lookup

Build:

```text
LAN A
192.168.10.0/24
      │
     R1
      │
     R2
      │
LAN B
10.10.10.0/24
```

Tasks:

1. Configure IP addressing.
2. Add a static route on R1 toward `10.10.10.0/24`.
3. Use a next-hop IP.
4. Verify the route.
5. Test connectivity.
6. Explain whether the next-hop requires recursive resolution.
7. Shut down the path and observe the effect.

---

## Lab 4 – Inter-VLAN Routing

Build:

```text
VLAN 10
192.168.10.0/24
       │
   Layer 3 Switch
       │
VLAN 20
192.168.20.0/24
```

Tasks:

1. Create VLAN 10 and VLAN 20.
2. Create SVIs.
3. Assign IP addresses to the SVIs.
4. Configure host default gateways.
5. Test VLAN 10 → VLAN 20 connectivity.
6. Verify the routing table.
7. Shut down one SVI and observe the result.

---

## Lab 5 – Routing Troubleshooting

Create intentional problems:

- Incorrect IP address.
- Incorrect subnet mask.
- Wrong default gateway.
- Missing route.
- Incorrect next hop.
- Interface shutdown.
- Missing return route.
- ACL blocking traffic.

Use:

```text
show ip interface brief
show ip route
ping
traceroute
```

Document:

```text
Problem
→ Evidence
→ Root Cause
→ Fix
→ Verification
```

---

# 🧠 Practical Design Challenge

You are designing the network for a financial organization with:

- Headquarters.
- Multiple branches.
- Data center.
- Internet connection.
- Multiple VLANs.
- Redundant Layer 3 gateways.

Requirements:

1. Users in different VLANs must communicate where permitted.
2. Branches must reach centralized applications.
3. Internet traffic must follow the intended default route.
4. Routing must remain available during a single link failure.
5. Troubleshooting must be straightforward.

Design:

1. Where would you place Layer 3 gateways?
2. Which networks would use connected routes?
3. Where would you use static routes?
4. Where would dynamic routing be more appropriate?
5. Where would you use a default route?
6. How would you provide redundancy?
7. How would you verify the routing path?
8. What would you check if a branch can reach headquarters but cannot reach the Internet?

---

# 📝 Chapter Summary

Routing connects different IP networks.

The core process is:

```text
Packet Arrives
      ↓
Destination IP
      ↓
Routing Table
      ↓
Longest Prefix Match
      ↓
Route Selection
      ↓
Next Hop / Interface
      ↓
Layer 2 Rewrite
      ↓
Forward Packet
```

The most important concepts are:

```text
Connected Routes
      ↓
Static Routes
      ↓
Dynamic Routes
      ↓
Default Route
      ↓
Longest Prefix Match
      ↓
Administrative Distance
      ↓
Metrics
      ↓
Recursive Lookup
      ↓
CEF / Forwarding
```

If you understand **why routing is required, how a router reads its routing table, how Longest Prefix Match works, how connected/static/dynamic routes differ, how packets move through a router, and how to troubleshoot routing failures**, you have a strong foundation for advanced routing.

---

# 🚀 Next Chapter

## Chapter 13 – Static Routing (Basic to Advanced)

We'll cover:

- What is Static Routing?
- Types of Static Routes
- Default Static Route
- Floating Static Route
- Recursive Route Lookup
- Black Hole Routes
- Route Summarization
- Administrative Distance
- Enterprise WAN Examples
- Cisco & FortiGate Configuration
- Troubleshooting
- Interview Questions
- Real-world use cases

This chapter builds directly on routing fundamentals and prepares you for dynamic routing protocols such as **RIP, OSPF, EIGRP, and BGP**.
