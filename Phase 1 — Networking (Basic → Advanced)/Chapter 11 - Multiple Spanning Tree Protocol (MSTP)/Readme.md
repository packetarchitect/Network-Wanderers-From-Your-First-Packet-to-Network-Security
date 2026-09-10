# 📘 Module 2 – Switching

# Chapter 11 – Multiple Spanning Tree Protocol (MSTP) (Basic to Advanced)

---

# 📚 What This Chapter Covers

- What is MSTP?
- Why MSTP Was Introduced
- IEEE 802.1s Standard
- STP vs RSTP vs MSTP
- MSTP Architecture
- MST Region
- Region Name
- Revision Number
- VLAN-to-Instance Mapping
- Internal Spanning Tree (IST)
- Common Spanning Tree (CST)
- Common and Internal Spanning Tree (CIST)
- MST Instances (MSTI)
- Root Bridge per Instance
- Load Balancing
- MSTP Packet Flow
- Enterprise Campus Design
- Configuration
- Verification Commands
- Troubleshooting
- Best Practices
- Interview Questions
- Hands-on Lab
- Key Takeaways
- Quick Revision Notes

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain why MSTP was introduced.
- Compare STP, RSTP, and MSTP.
- Understand MST regions and instances.
- Map VLANs to MST instances.
- Design scalable Layer 2 enterprise networks.
- Troubleshoot MST region mismatches.
- Explain MSTP confidently in interviews.

---

# 1. What is MSTP?

**MSTP (Multiple Spanning Tree Protocol)** is an enhancement of RSTP that allows **multiple VLANs to share the same spanning tree instance**, reducing CPU and memory usage while still preventing Layer 2 loops.

**IEEE Standard:**
```text
IEEE 802.1s
```

MSTP combines:

- **Rapid convergence** from RSTP.
- **Scalability** for networks with many VLANs.

### Interview Definition

> **MSTP is a Layer 2 loop-prevention protocol that extends RSTP by mapping multiple VLANs to a smaller number of spanning-tree instances, improving scalability and allowing load balancing across redundant links.**

---

# 2. Why Was MSTP Introduced?

Imagine a large company with:

- 200 VLANs
- 50 switches

If each VLAN has its own spanning tree calculation:

```text
VLAN 10  → STP Calculation
VLAN 20  → STP Calculation
VLAN 30  → STP Calculation
...
VLAN 200 → STP Calculation
```

This consumes significant:

- CPU
- Memory
- BPDU processing

## MSTP Solution

Instead of creating one spanning tree per VLAN, MSTP groups VLANs into **instances**.

```text
Instance 1
├── VLAN 10
├── VLAN 20
└── VLAN 30

Instance 2
├── VLAN 40
├── VLAN 50
└── VLAN 60

Instance 3
├── VLAN 100
└── VLAN 110
```

Now the switches calculate only **three spanning trees** instead of hundreds.

### Core Idea

```text
Many VLANs
    ↓
Grouped into MST Instances
    ↓
Fewer spanning-tree calculations
    ↓
Lower resource usage
    ↓
Better scalability
```

---

# 3. IEEE 802.1s Standard

MSTP is associated with:

```text
IEEE 802.1s
```

It extends the rapid spanning-tree approach so that multiple VLANs can be mapped to a smaller number of spanning-tree instances.

---

# 4. STP vs RSTP vs MSTP

| Feature | STP | RSTP | MSTP |
|---|---|---|---|
| IEEE | 802.1D | 802.1w | 802.1s |
| Convergence | Slow | Fast | Fast |
| Scalability | Low | Medium | High |
| VLAN Handling | Traditional STP topology | Rapid STP topology | Multiple VLANs per MST Instance |
| Enterprise Use | Legacy | Common | Large Enterprise/Campus |

### Simple Comparison

```text
STP
↓
Basic loop prevention
↓
Slow convergence

RSTP
↓
Rapid convergence
↓
Better failure recovery

MSTP
↓
Rapid convergence + VLAN grouping
↓
Scalable enterprise Layer 2 design
```

---

# 5. MSTP Architecture

MSTP introduces a hierarchy of spanning-tree concepts.

```text
MST Region
│
├── IST (Instance 0)
│
├── MSTI 1
│   ├── VLAN 10
│   ├── VLAN 20
│   └── VLAN 30
│
├── MSTI 2
│   ├── VLAN 40
│   └── VLAN 50
│
└── MSTI 3
    └── VLAN 100
```

Important terms:

- **MST Region**
- **IST**
- **MSTI**
- **CST**
- **CIST**

---

# 6. MST Region

An **MST Region** is a group of switches that share the same MST configuration.

For switches to belong to the same region, they must have:

- Same **Region Name**
- Same **Revision Number**
- Same **VLAN-to-Instance Mapping**

Example:

```text
Region Name: HQ-Network
Revision: 5

Instance 1 → VLAN 10,20,30
Instance 2 → VLAN 40,50
```

If these configuration parameters do not match, the switch is treated as belonging to a different MST region.

---

# 7. Region Name and Revision Number

## Region Name

Example:

```text
HQ-Network
```

The region name identifies the MST configuration domain.

## Revision Number

Example:

```text
Revision 5
```

The revision number is part of the MST configuration.

### Operational Rule

Maintain the same:

```text
Region Name
+
Revision
+
VLAN-to-Instance Mapping
```

across all switches intended to operate inside the same MST region.

---

# 8. MST Instances (MSTI)

An **MST Instance (MSTI)** is a logical spanning tree that manages one or more VLANs.

```text
MST Instance 1
├── VLAN 10
├── VLAN 20
└── VLAN 30

MST Instance 2
├── VLAN 40
└── VLAN 50
```

Each MSTI can have:

- Its own Root Bridge.
- Its own port roles.
- Its own forwarding topology.

---

# 9. Internal Spanning Tree (IST)

Every MST region has one **Internal Spanning Tree (IST)**.

Characteristics:

- Always **Instance 0**.
- Connects switches inside the MST region.
- Provides the internal spanning-tree structure for the region.
- Carries MST information between switches in the region.

Think of IST as the internal backbone of the MST region.

```text
MST Region
│
├── IST / Instance 0
├── MSTI 1
├── MSTI 2
└── MSTI 3
```

---

# 10. Common Spanning Tree (CST)

The **Common Spanning Tree (CST)** represents spanning-tree connectivity between MST regions.

```text
MST Region A
      │
      │ CST
      │
MST Region B
```

It can also provide connectivity between an MST region and external spanning-tree domains.

---

# 11. Common and Internal Spanning Tree (CIST)

The **Common and Internal Spanning Tree (CIST)** provides the common spanning-tree view across MST regions and within an MST region.

Conceptually:

```text
CIST
│
├── Internal spanning-tree structure
│   └── IST
│
└── Common spanning-tree connectivity
    └── CST
```

The CIST provides a consistent loop-free spanning-tree framework across the broader Layer 2 topology.

---

# 12. VLAN-to-Instance Mapping

One of the key features of MSTP is mapping multiple VLANs to the same MST instance.

```text
Instance 1
├── VLAN 10
├── VLAN 20
└── VLAN 30

Instance 2
├── VLAN 40
└── VLAN 50

Instance 3
└── VLAN 100
```

Benefits:

- Fewer spanning-tree calculations.
- Lower CPU utilization.
- Lower memory requirements.
- Better scalability.

### Design Principle

Do not create an MST instance for every VLAN unless there is a genuine topology requirement.

Instead:

```text
Related VLANs
     ↓
Group together
     ↓
Map to an MSTI
```

---

# 13. Root Bridge per Instance

MSTP allows different MST instances to have different Root Bridges.

Example:

```text
          Core A
         /      \
       SW1      SW2
         \      /
          Core B
```

Possible design:

```text
Instance 1 Root → Core A
Instance 2 Root → Core B
```

This means different VLAN groups can use different preferred Layer 2 paths.

---

# 14. Load Balancing

MSTP can use different Root Bridges for different MST instances.

```text
Instance 1 → Core A
Instance 2 → Core B
```

Result:

- VLAN 10 traffic can prefer Core A.
- VLAN 40 traffic can prefer Core B.

This distributes traffic across redundant Layer 2 paths.

**Important:** MSTP does not automatically mean all links carry equal traffic. Load balancing depends on instance-to-VLAN mapping, root placement, path costs, topology, and traffic patterns.

---

# 15. MSTP Packet Flow

For VLAN 20 mapped to MSTI 1:

```text
VLAN 20
   ↓
MSTI 1
   ↓
MSTI 1 Root Bridge
   ↓
MSTI 1 forwarding topology
   ↓
Destination
```

For VLAN 50 mapped to MSTI 2:

```text
VLAN 50
   ↓
MSTI 2
   ↓
MSTI 2 Root Bridge
   ↓
MSTI 2 forwarding topology
   ↓
Destination
```

Two VLANs can therefore follow different forwarding paths while sharing the same physical switching infrastructure.

---

# 16. Enterprise Campus Design

Consider a large campus with:

- Multiple buildings.
- Hundreds of VLANs.
- Redundant core and distribution links.
- Multiple access switches.

A scalable design might be:

```text
                 Core A
                /      \
             Dist1     Dist2
              |          |
            Access     Access
              \          /
                 Core B
```

Example mapping:

```text
MSTI 1
├── User VLANs
├── HR VLAN
└── Finance VLAN

MSTI 2
├── Server VLANs
├── Application VLANs
└── Management VLAN
```

Root placement:

```text
MSTI 1 → Core A
MSTI 2 → Core B
```

Benefits:

- Fewer spanning-tree instances.
- Rapid convergence.
- Logical traffic distribution.
- Predictable Layer 2 paths.
- Easier scaling than maintaining a separate tree for every VLAN.

---

# 17. Real-World Banking Example

A bank has:

```text
MSTI 1
├── HR VLAN
└── Finance VLAN

MSTI 2
├── ATM VLAN
└── Branch VLAN

MSTI 3
└── Guest VLAN
```

Possible root placement:

```text
MSTI 1 → Core A
MSTI 2 → Core B
MSTI 3 → Core A
```

The ATM network can prefer one core while office traffic prefers another.

This allows redundant Layer 2 infrastructure to be used more efficiently.

---

# 18. Cisco Configuration

## Enable MST Mode

```text
spanning-tree mode mst
```

## Configure MST

```text
spanning-tree mst configuration
 name HQ-Network
 revision 5
 instance 1 vlan 10,20,30
 instance 2 vlan 40,50
```

## Configure Root Priority

After configuring the MST region, root placement can be influenced with MST instance priority commands:

```text
spanning-tree mst 1 priority 24576
spanning-tree mst 2 priority 28672
```

A lower priority is preferred during Root Bridge election.

### Configuration Principle

All switches intended to belong to the same MST region should use the same:

```text
Region Name
Revision
VLAN-to-Instance Mapping
```

---

# 19. Verification Commands

## Check MST Status

```text
show spanning-tree mst
```

Useful for checking:

- MST instances.
- Root information.
- Port roles.
- Port states.

## View MST Configuration

```text
show spanning-tree mst configuration
```

Check:

- Region Name.
- Revision.
- VLAN mappings.
- Configuration digest.

## Display Interface Details

```text
show spanning-tree mst interface
```

Useful for investigating:

- Port role.
- Port state.
- Instance information.
- Interface participation.

## Check General Spanning-Tree Information

```text
show spanning-tree summary
```

---

# 20. Troubleshooting MSTP

## Problem 1: A Switch Does Not Join the Expected MST Region

Check:

```text
show spanning-tree mst configuration
```

Compare:

- Region Name.
- Revision Number.
- VLAN-to-Instance Mapping.
- Configuration digest.

### Troubleshooting Flow

```text
Switch not joining region
        ↓
Check MST mode
        ↓
Check Region Name
        ↓
Check Revision
        ↓
Check VLAN Mapping
        ↓
Compare Configuration Digest
        ↓
Correct Configuration
        ↓
Verify MST Topology
```

---

## Problem 2: Traffic Follows an Unexpected Path

Check:

```text
show spanning-tree mst
```

Verify:

- Root Bridge for each instance.
- Root ports.
- Designated ports.
- Path costs.
- VLAN-to-instance mapping.

---

## Problem 3: High CPU on Switches

Possible causes:

- Too many MST instances.
- Excessive topology changes.
- Unstable links.
- Incorrect MST design.
- Repeated Layer 2 failures.

Check topology stability before assuming MST itself is the problem.

---

## Problem 4: VLAN Uses the Wrong Forwarding Path

Check:

```text
show spanning-tree mst configuration
```

Then verify:

```text
VLAN
 ↓
MSTI
 ↓
Root Bridge
 ↓
Forwarding Path
```

---

# 21. MSTP Best Practices

- Keep the number of MST instances low.
- Group VLANs logically.
- Use meaningful Region Names.
- Configure Root Bridges intentionally.
- Maintain identical MST configurations across the region.
- Document VLAN-to-instance mappings.
- Plan primary and secondary root placement.
- Monitor topology changes.
- Verify the forwarding topology after configuration changes.
- Avoid unnecessary MST instances.
- Use redundant Layer 2 paths deliberately rather than accidentally.

### Recommended Design Pattern

```text
Core A
 ├── Root for MSTI 1
 └── Secondary for MSTI 2

Core B
 ├── Root for MSTI 2
 └── Secondary for MSTI 1
```

This allows controlled Layer 2 load distribution.

---

# 22. Common Interview Questions

### Q1. Why was MSTP introduced?

To reduce the number of spanning-tree calculations by allowing multiple VLANs to share a single spanning-tree instance.

### Q2. Which IEEE standard defines MSTP?

**IEEE 802.1s**

### Q3. What is an MST Instance?

A logical spanning tree that controls one or more VLANs.

### Q4. What must match for switches to join the same MST region?

The operational MST configuration must match, including:

- Region Name
- Revision Number
- VLAN-to-Instance Mapping

### Q5. What is the benefit of MSTP over RSTP?

RSTP provides rapid convergence, while MSTP additionally allows multiple VLANs to share a smaller number of spanning-tree instances, improving scalability and enabling per-instance load balancing.

### Q6. What is IST?

IST stands for **Internal Spanning Tree** and is **Instance 0**, providing the internal spanning-tree structure of an MST region.

### Q7. What is an MSTI?

MSTI stands for **Multiple Spanning Tree Instance**. It is a logical spanning tree used for a group of VLANs mapped to that instance.

### Q8. What is CIST?

CIST stands for **Common and Internal Spanning Tree** and provides the common spanning-tree framework across MST regions and within the MST region.

### Q9. How does MSTP provide load balancing?

By mapping VLAN groups to different MST instances and assigning different Root Bridges to those instances, different VLAN groups can prefer different Layer 2 paths.

### Q10. What happens if the MST region name matches but VLAN mapping does not?

The switches do not have the same MST configuration and are treated as being in different MST regions.

### Q11. Why should the number of MST instances be kept low?

Each additional instance creates additional spanning-tree state and processing. Grouping VLANs into a reasonable number of instances improves scalability.

### Q12. How would you troubleshoot an MST region mismatch?

I would compare the MST mode, region name, revision, VLAN-to-instance mapping, and configuration digest on neighboring switches, correct any mismatch, and then verify the resulting MST topology.

---

# 🎤 Interview Answer – Explain MSTP in 3–5 Minutes

> **"Multiple Spanning Tree Protocol, or MSTP, is a Layer 2 loop-prevention protocol associated with IEEE 802.1s. It extends the rapid spanning-tree approach by allowing multiple VLANs to be mapped to a smaller number of spanning-tree instances. This is particularly useful in large enterprise networks where maintaining a separate spanning tree for every VLAN would create unnecessary processing and operational overhead.**
>
> **MSTP organizes switches into MST regions. Switches intended to operate in the same region need matching MST configuration, including the region name, revision number, and VLAN-to-instance mapping. Within the region, Instance 0 is the Internal Spanning Tree, or IST. Additional MSTIs can contain groups of VLANs. The broader Common and Internal Spanning Tree, or CIST, provides the common spanning-tree framework across the network, including connectivity between regions.**
>
> **One of the major benefits of MSTP is scalability. For example, instead of creating separate spanning trees for 200 VLANs, an enterprise might map related VLANs into three or four MST instances. This reduces the amount of spanning-tree state while maintaining rapid convergence.**
>
> **MSTP also supports Layer 2 load balancing. Different MST instances can have different Root Bridges. For example, MSTI 1 can use Core A as its Root Bridge while MSTI 2 uses Core B. As a result, different groups of VLANs can prefer different redundant paths.**
>
> **On Cisco switches, MST is enabled with 'spanning-tree mode mst', followed by the MST configuration containing the region name, revision, and VLAN-to-instance mappings. Verification can be performed with commands such as 'show spanning-tree mst' and 'show spanning-tree mst configuration'.**
>
> **In summary, MSTP combines the rapid convergence characteristics of RSTP with scalable VLAN grouping and per-instance topology control, making it especially useful for large enterprise campus networks with many VLANs and redundant Layer 2 links."**

---

# 🧠 Memory Trick

Remember:

```text
MSTP = MANY VLANs → FEWER TREES
```

### Region

```text
NAME
 +
REVISION
 +
VLAN MAPPING
 =
MST REGION
```

### Instances

```text
VLAN 10 ─┐
VLAN 20 ─┼──→ MSTI 1
VLAN 30 ─┘

VLAN 40 ─┐
VLAN 50 ─┴──→ MSTI 2
```

### Important Terms

```text
IST  → Instance 0
MSTI → Additional spanning-tree instance
CST  → Common Spanning Tree
CIST → Common + Internal Spanning Tree
```

### Load Balancing

```text
MSTI 1 → Core A
MSTI 2 → Core B
```

Think:

> **Different VLAN groups → Different trees → Different preferred paths**

---

# ⭐ Gold Interview Tip

If the interviewer asks:

> **"What is the biggest advantage of MSTP?"**

Say:

> **"MSTP reduces the number of spanning-tree instances by allowing multiple VLANs to share a tree, while still providing rapid convergence and allowing different instances to use different Root Bridges for Layer 2 load balancing."**

Then explain:

**Region → VLAN Mapping → MSTI → Root Bridge → Load Balancing → Verification.**

---

# 📝 Quick Revision Notes

| Topic | Remember |
|---|---|
| MSTP | Multiple VLANs mapped to fewer spanning trees |
| Standard | IEEE 802.1s |
| Based On | RSTP |
| Main Goal | Scalability + loop prevention |
| MST Region | Matching MST configuration |
| Region Parameters | Name + Revision + VLAN Mapping |
| IST | Instance 0 |
| MSTI | Additional logical spanning-tree instance |
| CST | Common spanning tree between regions |
| CIST | Common and Internal Spanning Tree |
| Load Balancing | Different MSTIs can use different roots |
| Cisco Mode | `spanning-tree mode mst` |
| Verification | `show spanning-tree mst` |

---

# 🎯 Assignment – Test Yourself

## Theory

1. Compare STP, RSTP, and MSTP.
2. Explain why MSTP is preferred in large campus networks.
3. Define:
   - MST Region
   - MST Instance
   - IST
   - CST
   - CIST
4. Explain how VLAN-to-instance mapping reduces switch resource usage.
5. Describe how MSTP supports Layer 2 load balancing.
6. Explain why the MST region configuration must match between switches.
7. Explain the difference between IST and MSTI.
8. Explain how a VLAN reaches its forwarding topology through an MSTI.
9. Explain what happens when a switch has a different VLAN-to-instance mapping.
10. Give a 3–5 minute interview explanation of MSTP without looking at your notes.

---

# 🧪 Practical Assignment – Lab Practice

## Lab 1 – Basic MSTP Configuration

Build a topology in EVE-NG or Packet Tracer:

```text
         Core A
        /           Dist1    Dist2
        \      /
        Access
```

Tasks:

1. Enable MSTP.
2. Create:
   - Instance 1 → VLANs 10,20,30
   - Instance 2 → VLANs 40,50
3. Configure the same MST region name, revision, and mappings on all intended region members.
4. Configure Core A as the Root Bridge for Instance 1.
5. Configure Core B (or Dist2 in a smaller lab) as the Root Bridge for Instance 2.
6. Verify:
   - `show spanning-tree mst`
   - `show spanning-tree mst configuration`
7. Record the Root Bridge and port roles for each instance.

---

## Lab 2 – MST Region Mismatch

Create an intentional mismatch:

```text
SW1
Region: HQ-Network
Revision: 5

SW2
Region: Branch-Network
Revision: 5
```

Tasks:

1. Verify that the switches do not form the same MST region.
2. Compare:
   - Region Name
   - Revision
   - VLAN Mapping
   - Configuration Digest
3. Correct the mismatch.
4. Verify that the MST topology now behaves as expected.

---

## Lab 3 – MSTP Load Balancing

Create:

```text
          Core A
         /             Dist1    Dist2
         \      /
          Core B
```

Configure:

```text
MSTI 1 → VLAN 10,20,30
MSTI 2 → VLAN 40,50
```

Tasks:

1. Make Core A the preferred Root Bridge for MSTI 1.
2. Make Core B the preferred Root Bridge for MSTI 2.
3. Verify the forwarding paths.
4. Confirm that different VLAN groups prefer different paths.
5. Shut down a redundant link.
6. Observe reconvergence.
7. Verify the resulting topology.

---

## Lab 4 – MSTP Troubleshooting

Create intentional problems:

- Wrong Region Name.
- Wrong Revision.
- Wrong VLAN mapping.
- Unexpected Root Bridge.
- Unstable redundant link.

Use:

```text
show spanning-tree mst
show spanning-tree mst configuration
show spanning-tree mst interface
show spanning-tree summary
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

You are designing the switching layer for a large financial organization.

Requirements:

- 200+ VLANs.
- Two redundant core switches.
- Multiple distribution switches.
- Multiple access switches.
- Fast Layer 2 recovery.
- Controlled traffic distribution across redundant links.
- Simple and scalable spanning-tree management.

Design:

1. How many MST instances would you create?
2. Which VLANs would you group together?
3. Which core switch should be Root for each MSTI?
4. Which core switch should be Secondary Root?
5. How would you keep MST region configuration consistent?
6. How would you verify the design after deployment?
7. How would you troubleshoot an unexpected forwarding path?
8. How would you test failure recovery?

---

# 📝 Chapter Summary

MSTP solves a major scalability problem in large Layer 2 networks.

The core concept is:

```text
Many VLANs
     ↓
Group VLANs
     ↓
MST Instances
     ↓
Fewer spanning trees
     ↓
Lower resource usage
     ↓
Rapid convergence
     ↓
Different roots per instance
     ↓
Layer 2 load balancing
```

The most important concepts to remember are:

```text
IEEE 802.1s
      ↓
MST Region
      ↓
Name + Revision + VLAN Mapping
      ↓
IST / Instance 0
      ↓
MSTIs
      ↓
VLAN-to-Instance Mapping
      ↓
Root Bridge per Instance
      ↓
Load Balancing
```

If you understand **why MSTP exists, how regions are formed, how VLANs are mapped to MSTIs, what IST/CST/CIST mean, how Root Bridges are selected per instance, and how MSTP provides scalable Layer 2 load balancing**, you understand the core of MSTP.

---

# 🚀 Next Chapter

## Chapter 12 – Routing Fundamentals (Basic to Advanced)

Now that you've mastered **Layer 2 switching and spanning-tree technologies**, we'll move into **Layer 3 routing**, where you'll learn:

- What routing is.
- How routers make forwarding decisions.
- Routing tables.
- Longest Prefix Match.
- Administrative Distance.
- Metrics.
- Static vs Dynamic Routing.
- Packet flow through a router.
- Real enterprise routing designs.
- Troubleshooting routing issues.

This marks the transition from **Switching → Routing**, which is essential before learning **OSPF, BGP, VPNs, and enterprise firewall packet flow**.
