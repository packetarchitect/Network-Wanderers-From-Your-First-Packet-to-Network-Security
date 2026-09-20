# 📘 Module 3 – Routing

# Chapter 16 – OSPF (Open Shortest Path First) (Basic to Advanced)

---

# 📚 What This Chapter Covers

- What is OSPF?
- Why OSPF Was Introduced
- History & RFC
- Link-State Routing
- OSPF Architecture
- Areas
- Backbone Area (Area 0)
- Router IDs
- Neighbor Discovery
- OSPF Neighbor States
- Hello Packets
- DR & BDR Election
- OSPF Packet Types
- Link State Advertisements (LSAs)
- Link State Database (LSDB)
- SPF (Dijkstra) Algorithm
- OSPF Cost Metric
- Route Calculation
- OSPF Route Types
- Single Area vs Multi-Area OSPF
- Stub Areas
- Totally Stubby Areas
- NSSA (Not-So-Stubby Area)
- Virtual Links
- Route Summarization
- Authentication
- Equal Cost Multi-Path (ECMP)
- OSPF Packet Flow
- Cisco Configuration
- FortiGate OSPF Configuration
- Verification Commands
- Troubleshooting
- Enterprise Design
- Real-World Examples
- Common Interview Questions
- Hands-on Labs
- Key Takeaways
- Quick Revision Notes

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain how OSPF works.
- Understand link-state routing.
- Configure OSPF on Cisco and FortiGate.
- Explain DR/BDR elections and neighbor formation.
- Understand LSAs and the SPF algorithm.
- Design multi-area OSPF networks.
- Troubleshoot OSPF issues.
- Confidently answer OSPF interview questions.

---

# 1. What is OSPF?

**OSPF (Open Shortest Path First)** is a **Link-State Interior Gateway Protocol (IGP)** used to exchange routing information within an Autonomous System (AS).

Unlike RIP, which only knows the next hop and hop count, OSPF builds a **complete map (topology)** of the network.

### RFC

```
RFC 2328
```

### Interview Definition

> **OSPF is a link-state routing protocol that uses Dijkstra's Shortest Path First (SPF) algorithm to calculate the shortest path and exchange routing information efficiently within an autonomous system.**

---

# 2. Why Was OSPF Introduced?

RIP has several limitations:

- Maximum 15 hops
- Slow convergence
- Periodic updates
- Poor scalability

Imagine a bank with:

```
200 Branches

↓

MPLS

↓

Head Office

↓

Data Center
```

RIP would converge slowly and struggle to scale.

OSPF solves this by:

- Fast convergence
- Hierarchical design
- Efficient updates
- Better scalability

---

# 3. Why is OSPF Called a Link-State Protocol?

Instead of sharing the entire routing table, OSPF routers exchange **Link-State Advertisements (LSAs)**.

Each router learns:

- All routers
- All links
- Costs
- Complete topology

Every router builds the same **Link-State Database (LSDB)** and independently calculates the best paths using the SPF algorithm.

---

# 4. OSPF Architecture

```
        Area 0 (Backbone)
   R1 ------- R2 ------- R3
              │
          Area 10
              │
             R4
              │
          Area 20
              │
             R5
```

All non-backbone areas must connect to **Area 0** either directly or through a virtual link.

---

# 5. Autonomous System (AS)

An **Autonomous System (AS)** is a group of routers managed by a single organization using a common routing policy.

Examples:

- A company network
- A university network
- A bank's internal network

OSPF operates **within** an AS (Interior Gateway Protocol).

BGP is used **between** ASes (Exterior Gateway Protocol).

---

# 6. OSPF Areas

An **Area** divides a large OSPF network into smaller sections to improve scalability.

Benefits:

- Smaller LSDB
- Faster SPF calculations
- Lower CPU and memory usage
- Reduced routing updates

Example:

| Area    | Purpose        |
| ------- | -------------- |
| Area 0  | Backbone       |
| Area 10 | Branch Offices |
| Area 20 | Data Center    |
| Area 30 | Manufacturing  |

---

# 7. Backbone Area (Area 0)

Area 0 is mandatory in OSPF.

```
Area 10

↓

Area 0

↓

Area 20
```

Traffic between non-backbone areas passes through Area 0.

---

# 8. Router ID (RID)

Each OSPF router requires a unique **32-bit Router ID**.

Selection priority:

1. Manually configured Router ID
2. Highest Loopback IP
3. Highest Active Physical Interface IP

Example:

```
Router ID

1.1.1.1
```

---

# 9. Neighbor Discovery

OSPF routers discover neighbors using **Hello Packets**.

```
Router A

⇄ Hello ⇄

Router B
```

Hello packets verify:

- Area ID
- Hello Timer
- Dead Timer
- Authentication
- Network Mask (on broadcast/NBMA)
- Stub Area Flag

If these parameters don't match, adjacency will not form.

---

# 10. OSPF Neighbor States

OSPF routers pass through several states:

```
Down
  ↓
Init
  ↓
2-Way
  ↓
ExStart
  ↓
Exchange
  ↓
Loading
  ↓
Full
```

### Meaning

- **Down** – No Hello packets received.
- **Init** – Hello received, but no two-way communication.
- **2-Way** – Bidirectional communication established.
- **ExStart** – Master/Slave negotiation begins.
- **Exchange** – Database Description (DBD) packets exchanged.
- **Loading** – LSAs requested and received.
- **Full** – LSDB synchronized.

---

# 11. OSPF Packet Types

OSPF uses five packet types.

| Packet                            | Purpose                            |
| --------------------------------- | ---------------------------------- |
| Hello                             | Neighbor discovery and maintenance |
| Database Description (DBD)        | Summarizes LSDB                    |
| Link State Request (LSR)          | Requests missing LSAs              |
| Link State Update (LSU)           | Sends LSAs                         |
| Link State Acknowledgment (LSAck) | Confirms LSU receipt               |

---

# 12. Link State Advertisements (LSAs)

LSAs describe the network topology.

Examples:

- Router information
- Connected networks
- Costs
- Area information

These LSAs populate the **Link-State Database (LSDB)**.

---

# 13. Link-State Database (LSDB)

Every OSPF router within the same area should have an identical LSDB.

```
Router A LSDB
        =
Router B LSDB
        =
Router C LSDB
```

The LSDB is then used to calculate the shortest paths.

---

# 14. SPF (Dijkstra) Algorithm

OSPF uses **Dijkstra's Shortest Path First (SPF)** algorithm.

Example:

```
      R2
     /  \
R1        R4
     \  /
      R3
```

The algorithm calculates the lowest-cost path from R1 to every destination.

---

# 15. OSPF Cost Metric

OSPF uses **Cost** instead of hop count.

Default Cisco formula:

```
Cost = Reference Bandwidth / Interface Bandwidth
```

Default reference bandwidth:

```
100 Mbps
```

Examples:

| Interface | Cost (Default) |
| --------- | -------------- |
| 10 Mbps   | 10             |
| 100 Mbps  | 1              |
| 1 Gbps    | 1\*            |

\* On modern networks, administrators usually increase the reference bandwidth so Gigabit and faster links receive lower costs than Fast Ethernet.

---

# 16. DR & BDR Election

On broadcast networks (such as Ethernet), OSPF elects:

- **Designated Router (DR)**
- **Backup Designated Router (BDR)**

Purpose:

- Reduce the number of OSPF adjacencies.
- Improve scalability.

Example:

```
        Switch
      /   |   \
    R1   R2   R3
```

Without DR:

Each router forms adjacencies with every other router.

With DR:

All routers primarily synchronize through the DR.

---

# 17. OSPF Route Types

| Route Type           | Meaning                                          |
| -------------------- | ------------------------------------------------ |
| Intra-Area           | Within same area                                 |
| Inter-Area           | Between OSPF areas                               |
| External Type 1 (E1) | External route including internal cost           |
| External Type 2 (E2) | External route where external metric is dominant |

---

# 18. Single Area vs Multi-Area OSPF

### Single Area

```
Area 0

R1—R2—R3
```

Suitable for small networks.

---

### Multi-Area

```
Area10

↓

Area0

↓

Area20
```

Suitable for large enterprises.

---

# 19. Stub Areas

Stub areas reduce routing information by blocking most external routes.

Types include:

- Stub Area
- Totally Stubby Area (Cisco feature)
- NSSA (Not-So-Stubby Area)

These optimize large OSPF deployments.

---

# 20. Totally Stubby Areas

A **Totally Stubby Area** is a Cisco-specific OSPF feature that further restricts external and inter-area route information entering the area, commonly using a default route instead.

This can simplify routing information for branch-style areas.

---

# 21. NSSA — Not-So-Stubby Area

An **NSSA** is designed for an area that should remain relatively stub-like while still allowing certain external routes to be introduced into the OSPF domain.

This is useful when an area contains an external routing source or redistribution point.

```text
External Source
      ↓
    NSSA
      ↓
   Area 0
```

---

# 22. Virtual Links

A virtual link can provide a logical OSPF connection to Area 0 through another area when normal backbone connectivity is not available.

```text
Area 0
  |
Area 10
  |
Area 20
```

Virtual links are generally a workaround for specific designs rather than the preferred approach for a clean new OSPF architecture.

---

# 23. Route Summarization

Route summarization combines multiple more-specific networks into a larger prefix when the addressing is contiguous and correctly aligned.

Benefits include:

- Smaller routing tables
- Reduced routing information
- Better scalability
- Reduced impact of topology changes

Summarization is especially useful in multi-area OSPF designs.

---

# 24. Equal Cost Multi-Path (ECMP)

OSPF can support **Equal Cost Multi-Path (ECMP)** routing when multiple paths have the same OSPF cost.

Example:

```text
          R2
         /  \
        /    \
       R1    R3
        \    /
         \  /
          R4
```

If two paths have the same cost, both may be installed/used according to platform capabilities and configuration.

Benefits include:

- Load sharing
- Better link utilization
- Redundancy

---

# 25. OSPF Packet Flow

A simplified OSPF operation looks like:

```text
1. Neighbor Discovery
        ↓
2. Hello Packets
        ↓
3. Neighbor State Progression
        ↓
4. Database Synchronization
        ↓
5. LSAs
        ↓
6. LSDB
        ↓
7. SPF Calculation
        ↓
8. Best Routes
        ↓
9. Routing Table
```

---

# 26. Authentication

OSPF supports authentication to prevent unauthorized routers from joining.

Common methods:

- Simple Password
- MD5 Authentication
- Cryptographic authentication (OSPFv3 enhancements)

---

# 27. Cisco Configuration

```
router ospf 1
 router-id 1.1.1.1
 network 192.168.10.0 0.0.0.255 area 0
 network 10.1.1.0 0.0.0.3 area 0
```

---

# 28. FortiGate OSPF Configuration

CLI:

```
config router ospf
set router-id 1.1.1.1

config area
edit 0.0.0.0
next
end

config network
edit 1
set prefix 192.168.10.0 255.255.255.0
set area 0.0.0.0
next
end
```

---

# 29. Verification Commands

### Cisco

```
show ip ospf neighbor
```

```
show ip ospf interface
```

```
show ip ospf database
```

```
show ip route ospf
```

---

### FortiGate

```
get router info ospf neighbor
```

```
get router info ospf interface
```

```
get router info ospf database
```

```
get router info routing-table all
```

---

# 30. Troubleshooting

### Problem

Neighbor not forming.

Check:

- Area ID
- Hello/Dead Timers
- Authentication
- Router ID uniqueness
- Interface status
- MTU mismatch (common cause during ExStart/Exchange)
- Network type

---

### Problem

Routes missing.

Check:

- Network statements
- Area assignment
- LSDB synchronization
- Route filtering
- Interface configuration

---

# 31. Enterprise Example

A multinational company:

```
Branches

↓

Regional Offices

↓

Area 0

↓

Data Center

↓

Cloud
```

- Branches → Area 10
- Data Center → Area 20
- Head Office → Area 0

OSPF provides:

- Fast convergence
- Efficient routing
- Scalability
- Route summarization

---

# 32. Advantages & Disadvantages

## Advantages

- Fast convergence
- Highly scalable
- Classless (supports VLSM/CIDR)
- Efficient updates
- Open standard
- Supports ECMP

---

## Disadvantages

- More complex than RIP
- Requires more CPU and memory
- Proper area design is important

---

# 33. Common Interview Questions

### Q1. What type of routing protocol is OSPF?

A **Link-State Interior Gateway Protocol (IGP)**.

---

### Q2. Which algorithm does OSPF use?

**Dijkstra's Shortest Path First (SPF)** algorithm.

---

### Q3. What metric does OSPF use?

**Cost**.

---

### Q4. Why is Area 0 required?

Area 0 is the OSPF backbone. All non-backbone areas should connect to Area 0 for inter-area routing.

---

### Q5. What is the purpose of the DR and BDR?

To reduce the number of OSPF adjacencies and improve efficiency on broadcast networks.

---

### Q6. What are the five OSPF packet types?

- Hello
- Database Description (DBD)
- Link State Request (LSR)
- Link State Update (LSU)
- Link State Acknowledgment (LSAck)

---

# 🎤 Interview Answer – Explain OSPF in 3–5 Minutes

> **"OSPF, or Open Shortest Path First, is a Link-State Interior Gateway Protocol defined in RFC 2328. It uses Dijkstra's Shortest Path First algorithm to calculate the shortest path based on interface cost rather than hop count. OSPF routers exchange Hello packets to discover neighbors and synchronize their Link-State Databases using DBD, LSR, LSU, and LSAck packets. Every router within the same area maintains a consistent LSDB and independently computes the best routes. OSPF supports hierarchical network design through areas, with Area 0 acting as the backbone. It converges much faster than RIP and is widely used in enterprise networks because of its scalability, efficient updates, multi-area design, authentication, route summarization, ECMP, and support for different route types."**

---

# 🧠 Memory Trick

### OSPF Core Flow

```text
Hello
  ↓
Neighbor
  ↓
LSA
  ↓
LSDB
  ↓
SPF
  ↓
Route
```

### Five Packet Types

```text
H → D → R → U → A

Hello
DBD
LSR
LSU
LSAck
```

Think:

> **Discover → Describe → Request → Update → Acknowledge**

### Neighbor States

```text
Down
 ↓
Init
 ↓
2-Way
 ↓
ExStart
 ↓
Exchange
 ↓
Loading
 ↓
Full
```

---

# ⭐ Gold Interview Tip

When explaining OSPF in an interview, don't just list commands or definitions.

Explain the complete control-plane process:

```text
Neighbor Discovery
       ↓
Adjacency
       ↓
LSA Exchange
       ↓
LSDB Synchronization
       ↓
SPF Calculation
       ↓
Best Route
       ↓
Routing Table
```

That demonstrates that you understand **how OSPF actually works**, not just how to configure it.

---

# 📝 Quick Revision Notes

```text
OSPF
↓
Link-State IGP
↓
RFC 2328
↓
Dijkstra SPF
↓
Metric = Cost
↓
Area 0 = Backbone
```

### Neighbor States

```text
Down
Init
2-Way
ExStart
Exchange
Loading
Full
```

### Packet Types

```text
Hello
DBD
LSR
LSU
LSAck
```

### Core Database Flow

```text
LSAs
 ↓
LSDB
 ↓
SPF
 ↓
Routing Table
```

### Key Features

```text
Multi-Area
DR/BDR
Route Summarization
Authentication
ECMP
Stub Areas
NSSA
Virtual Links
```

### Cisco Verification

```text
show ip ospf neighbor
show ip ospf interface
show ip ospf database
show ip route ospf
```

### FortiGate Verification

```text
get router info ospf neighbor
get router info ospf interface
get router info ospf database
get router info routing-table all
```

---

# 🎯 Assignment – Test Yourself

## Theory

1. What is OSPF?
2. Why is OSPF called a Link-State protocol?
3. Which RFC defines OSPFv2?
4. What is an Autonomous System?
5. What is an OSPF Area?
6. Why is Area 0 important?
7. What is an OSPF Router ID?
8. Explain the OSPF neighbor states.
9. What are the five OSPF packet types?
10. What is an LSA?
11. What is an LSDB?
12. Explain the difference between an LSDB and routing table.
13. Which algorithm does OSPF use?
14. What metric does OSPF use?
15. Explain DR and BDR.
16. Compare single-area and multi-area OSPF.
17. What are Stub Areas?
18. What is a Totally Stubby Area?
19. What is NSSA?
20. What is an OSPF virtual link?
21. What is route summarization?
22. What is OSPF authentication?
23. What is ECMP?
24. Explain E1 and E2 routes.
25. Why is OSPF more scalable than RIP?

## Scenario Questions

### Scenario 1 — Neighbor Not Forming

Two routers are directly connected, but they remain in:

```text
Down / Init
```

What would you check?

---

### Scenario 2 — ExStart Problem

Two routers reach:

```text
ExStart
```

but do not progress.

What common issue would you investigate first?

---

### Scenario 3 — Routes Missing

The OSPF neighbor is:

```text
FULL
```

but a remote network is missing from the routing table.

What would you investigate?

---

### Scenario 4 — Multi-Area Design

You have:

```text
Area 10
   |
Area 0
   |
Area 20
```

Explain how a route from Area 10 can reach a destination in Area 20.

---

# 🧪 Practical Assignment – Lab Practice

## Lab 1 — Basic Single-Area OSPF

Build:

```text
          R2
         /  \
       R1    R3
         \  /
          R4
```

Tasks:

1. Configure IP addressing.
2. Configure OSPF Area 0.
3. Configure unique Router IDs.
4. Verify neighbors.

Commands:

```text
show ip ospf neighbor
show ip ospf interface
show ip ospf database
show ip route ospf
```

---

## Lab 2 — OSPF Neighbor States

Build two directly connected routers.

Tasks:

1. Configure OSPF.
2. Observe the neighbor relationship.
3. Understand the progression:

```text
Down
→ Init
→ 2-Way
→ ExStart
→ Exchange
→ Loading
→ Full
```

Use appropriate verification commands and deliberately introduce a mismatch to practice troubleshooting.

---

## Lab 3 — Multi-Area OSPF

Build:

```text
          Area 10
             |
             |
          Area 0
             |
             |
          Area 20
```

Tasks:

1. Configure Area 0.
2. Configure Area 10.
3. Configure Area 20.
4. Verify inter-area routes.
5. Examine the OSPF database.
6. Test end-to-end connectivity.

---

## Lab 4 — DR/BDR

Connect multiple routers through an Ethernet multi-access network:

```text
        Switch
      /   |   \
    R1   R2   R3
```

Tasks:

1. Configure OSPF.
2. Identify DR.
3. Identify BDR.
4. Observe neighbor relationships.
5. Change OSPF priority and observe the effect where supported.
6. Verify the result.

---

## Lab 5 — OSPF Failure and Convergence

Build a redundant topology:

```text
        R2
       /  \
      R1   R4
       \  /
        R3
```

Tasks:

1. Configure OSPF.
2. Verify routes.
3. Shut down one link.
4. Observe route changes.
5. Test connectivity.
6. Review OSPF neighbor and routing-table changes.

---

## Lab 6 — Wireshark OSPF Analysis

Capture OSPF traffic.

Identify:

- Hello
- DBD
- LSR
- LSU
- LSAck

Connect the capture to the OSPF process:

```text
Hello
  ↓
Neighbor
  ↓
DBD / LSR / LSU / LSAck
  ↓
LSDB
  ↓
SPF
  ↓
Routing Table
```

---

# 🧠 Practical Design Challenge

Design an enterprise OSPF topology:

```text
                Area 0
             /         \
            /           \
       Area 10          Area 20
       Branches        Data Center
```

Requirements:

- Area 0 is the backbone.
- Area 10 contains branch networks.
- Area 20 contains data-center networks.
- Use unique Router IDs.
- Configure OSPF neighbors.
- Verify LSDB synchronization.
- Use route summarization where appropriate.
- Provide redundancy between important sites.
- Test convergence after a link failure.

### Design Questions

1. Which routers should act as ABRs?
2. Where would you use summarization?
3. Which links should have lower OSPF cost?
4. Where could ECMP be useful?
5. Where would authentication be appropriate?
6. Would you use a stub/NSSA design for any branch area?
7. How would you troubleshoot a branch that cannot reach the data center?

---

# 📝 Chapter Summary

OSPF is a **Link-State Interior Gateway Protocol** designed for scalable routing within an Autonomous System.

The fundamental OSPF process is:

```text
Neighbor Discovery
        ↓
Adjacency
        ↓
LSA Exchange
        ↓
LSDB
        ↓
Dijkstra SPF
        ↓
Best Routes
        ↓
Routing Table
```

The most important OSPF concepts to remember are:

```text
Link-State
Dijkstra
Cost
Area 0
Router ID
Neighbors
LSAs
LSDB
DR/BDR
Multi-Area
ECMP
Summarization
Authentication
```

OSPF provides significantly more scalability and flexibility than RIP, but that capability comes with additional design and troubleshooting complexity.

---

# 🚀 Next Chapter

## **Chapter 17 – BGP (Border Gateway Protocol) – Basic to Advanced**

We'll cover:

- What is BGP?
- IGP vs EGP
- Autonomous Systems (AS)
- eBGP vs iBGP
- BGP Neighbor Formation
- Path Attributes (AS\_PATH, LOCAL\_PREF, MED, NEXT\_HOP, WEIGHT)
- BGP Best Path Selection
- Route Reflection
- Confederations
- Internet Routing
- MPLS and ISP Use Cases
- Cisco & FortiGate Configuration
- Troubleshooting
- Interview Questions
- Enterprise and Service Provider Examples

This chapter completes the core routing protocols and prepares you for advanced enterprise and service provider networking.

ChatGPT can make mistakes. Check important info.

---

# 🌐 Keep Wandering

You have now moved from:

```text
Static Routing
      ↓
Dynamic Routing
      ↓
RIP — Distance Vector
      ↓
OSPF — Link State
```

The next major step is:

```text
BGP — Path Vector / Inter-Domain Routing
```

Don't just memorize OSPF states and commands.

Understand the complete journey:

> **Hello → Neighbor → LSA → LSDB → SPF → Route**

That understanding will make advanced routing and firewall routing much easier.
