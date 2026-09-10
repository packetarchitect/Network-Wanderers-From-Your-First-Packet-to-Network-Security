# 📘 Module 2 – Switching

# Chapter 10 – Rapid Spanning Tree Protocol (RSTP) (Basic to Advanced)

---

# 📚 What This Chapter Covers

- What is RSTP?
- Why RSTP Was Introduced
- IEEE 802.1w Standard
- STP vs RSTP
- How RSTP Works
- Root Bridge Election
- Bridge ID (BID)
- BPDU Processing
- RSTP Port Roles
- RSTP Port States
- Edge Ports
- Link Types
- Proposal & Agreement Mechanism
- Topology Changes
- Rapid Convergence
- RSTP Timers
- Enterprise Design
- Packet Flow
- Real-World Enterprise Scenarios
- Failure & Recovery
- Cisco Configuration
- Verification Commands
- Troubleshooting
- Best Practices
- Common Interview Questions
- 3–5 Minute Interview Answer
- Hands-on Labs
- Key Takeaways
- Quick Revision Notes

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain why RSTP replaced traditional STP.
- Compare STP and RSTP.
- Understand RSTP convergence.
- Identify all RSTP port roles and states.
- Explain Proposal/Agreement.
- Configure and troubleshoot RSTP.
- Answer advanced interview questions confidently.

---

# 1. What is RSTP?

**RSTP (Rapid Spanning Tree Protocol)** is an enhanced version of **Spanning Tree Protocol (STP)** that provides much faster convergence after a topology change while still preventing Layer 2 loops.

**IEEE Standard:**
```text
IEEE 802.1w
```

RSTP maintains a **loop-free Layer 2 topology** but recovers from failures much faster than traditional STP.

---

# 2. Why Was RSTP Introduced?

The biggest weakness of classic STP (IEEE 802.1D) was its **slow convergence**.

Typical STP convergence:
```text
30–50 Seconds
```

In an enterprise network, this delay can be unacceptable.

Example:
```text
             Core Switch
            /           \
      Distribution1   Distribution2
             \           /
            Access Switch
```

Suppose the link between the Core Switch and Distribution1 fails.

With STP:

- The backup link stays blocked.
- STP waits for multiple timers to expire.
- Users may experience 30–50 seconds of downtime.

Applications affected can include:

- VoIP calls
- Video meetings
- Banking transactions
- ERP systems

## RSTP Solution

RSTP introduces rapid convergence.

Typical recovery time:
```text
1–6 Seconds
```

Some direct link failures can recover in **less than one second**, depending on the topology and implementation.

---

# 3. IEEE 802.1w Standard

RSTP is defined by:

```text
IEEE 802.1w
```

It enhances the original IEEE 802.1D Spanning Tree Protocol while preserving the fundamental goal of preventing Layer 2 loops.

---

# 4. STP vs RSTP

| Feature | STP | RSTP |
|---|---|---|
| IEEE Standard | 802.1D | 802.1w |
| Convergence | 30–50 sec | 1–6 sec typically |
| Port Roles | Root, Designated + Blocking behavior | Root, Designated, Alternate, Backup |
| Port States | 5 | 3 |
| Proposal/Agreement | ❌ | ✅ |
| BPDU Generation | Root bridge originates BPDUs | Every switch generates BPDUs |
| Enterprise Use | Legacy | Modern standard |

**Key idea:** RSTP keeps the loop-prevention logic of STP but improves how switches detect failures and activate redundant paths.

---

# 5. How Does RSTP Work?

RSTP still performs the same basic tasks as STP:

1. Elect a Root Bridge.
2. Select the best path to the Root Bridge.
3. Block/discard redundant paths.
4. Prevent Layer 2 loops.
5. Rapidly react when topology changes.

The major difference is **how quickly** it reacts to topology changes.

---

# 6. Root Bridge Election

The election process is identical to STP.

Every switch has a **Bridge ID (BID)**.

```text
Bridge ID
    │
    ├── Bridge Priority
    └── MAC Address
```

The switch with the lowest Bridge ID wins the election.

Example:

| Switch | Priority | MAC Address |
|---|---:|---|
| SW1 | 32768 | 00:11:22:33:44:01 |
| SW2 | 32768 | 00:11:22:33:44:02 |
| SW3 | 4096 | 00:11:22:33:44:FF |

Result:

✅ **SW3 becomes the Root Bridge** because it has the lowest priority.

---

# 7. RSTP Port Roles

RSTP defines four important port roles.

## 7.1 Root Port (RP)

- One per non-root switch.
- Best path toward the Root Bridge.
- Forwards traffic toward the Root Bridge.
- Normally operates in the Forwarding state.

## 7.2 Designated Port (DP)

- One per LAN segment.
- Provides the best path from that segment toward the Root Bridge.
- Normally operates in the Forwarding state.

## 7.3 Alternate Port

The Alternate Port provides a **backup path toward the Root Bridge**.

Normally:

```text
Alternate Port
      │
      ↓
Discarding
```

If the Root Port fails:

```text
Root Port fails
      ↓
Alternate Port becomes Root Port
      ↓
Forwarding
```

This enables rapid recovery.

## 7.4 Backup Port

A Backup Port provides redundancy when **two ports on the same switch connect to the same shared network segment**.

This is uncommon in modern switched Ethernet but remains part of the RSTP model.

---

# 8. RSTP Port States

Classic STP had five states.

RSTP simplifies them to three:

| STP State | RSTP State |
|---|---|
| Disabled | Disabled |
| Blocking | Discarding |
| Listening | Discarding |
| Learning | Learning |
| Forwarding | Forwarding |

The important simplification is that **Blocking and Listening are represented by Discarding** in RSTP.

---

# 9. Edge Ports

An **Edge Port** connects directly to an end device.

```text
Laptop
   │
   ↓
Switch
```

Because an end device is not expected to create a Layer 2 switching loop, an edge port can transition immediately to Forwarding.

Cisco configuration:

```text
interface GigabitEthernet0/1
 spanning-tree portfast
```

### Important Security Point

PortFast should be used only on ports connected to end devices.

It should **not** normally be enabled on switch-to-switch links.

BPDU Guard is commonly paired with PortFast to protect an edge/access port from unexpected BPDUs.

---

# 10. Link Types

RSTP classifies links according to their characteristics.

## 10.1 Point-to-Point

```text
Switch A
    │
Switch B
```

This is common for full-duplex switch-to-switch links and supports rapid transitions.

## 10.2 Edge Port

```text
Laptop
   │
Switch
```

An edge port connects to an end device and can immediately transition to Forwarding.

## 10.3 Shared Link

```text
Switch
   │
  Hub
   │
Multiple Devices
```

Shared Ethernet using a hub is rare in modern enterprise networks. Rapid convergence is limited compared with full-duplex point-to-point links.

---

# 11. Proposal & Agreement Mechanism

This is one of the major improvements introduced by RSTP.

Conceptually:

```text
Switch A
   │
   │ Proposal
   ↓
Switch B
   │
   │ Checks for loop-free conditions
   ↓
Agreement
   │
   ↓
Rapid Forwarding
```

Instead of relying only on the long timer-based transition process associated with classic STP, RSTP can use a **Proposal/Agreement handshake** to rapidly establish a safe forwarding state.

### Interview Point

If asked:

> "Why does RSTP converge faster?"

A strong answer is:

> "RSTP uses rapid port transitions, alternate backup paths, edge-port behavior, and the Proposal/Agreement mechanism rather than depending on the traditional STP timer sequence for every topology change."

---

# 12. BPDU Processing

In classic STP:

- The Root Bridge originates BPDUs.
- Other switches process and propagate the spanning-tree information.

In RSTP:

**Every switch generates BPDUs every Hello interval.**

Advantages include:

- Faster failure detection.
- Faster topology updates.
- Better convergence behavior.

RSTP also uses BPDUs to maintain neighbor relationships and participate actively in the rapid convergence process.

---

# 13. Failure & Recovery

Consider:

```text
          Core
         /    \
      Dist1  Dist2
         \    /
        Access
```

Primary path:

```text
Core → Dist1 → Access
```

Backup path:

```text
Core → Dist2 → Access
```

The backup path can be represented by an **Alternate Port**.

If the primary Root Port fails:

```text
Primary Link Down
      ↓
Alternate Port selected
      ↓
Alternate becomes Root Port
      ↓
Forwarding
      ↓
Traffic resumes
```

The exact recovery time depends on the topology, link type, failure mechanism, and implementation.

---

# 14. Enterprise Example

A financial institution has redundant uplinks between access and distribution switches.

During business hours:

- One fiber link is accidentally cut.
- RSTP rapidly activates the alternate path.
- ATMs, trading terminals, and employee workstations can continue operating with minimal disruption.

Without RSTP, users may experience a longer interruption while classic STP reconverges.

### Design Principle

Redundancy is valuable only when the network can activate the backup path quickly and safely.

RSTP helps convert Layer 2 redundancy into practical high availability.

---

# 15. Cisco Configuration

## Enable Rapid PVST+

```text
spanning-tree mode rapid-pvst
```

## Configure PortFast on an End-Device Port

```text
interface GigabitEthernet0/10
 spanning-tree portfast
```

## Recommended Access-Port Protection

```text
interface GigabitEthernet0/10
 spanning-tree portfast
 spanning-tree bpduguard enable
```

Use BPDU Guard only where the port is intended to be an edge/access port.

---

# 16. Verification Commands

## Check STP Status

```text
show spanning-tree
```

Useful for checking:

- Root Bridge
- Root Port
- Designated Ports
- Port states
- Path cost
- Bridge priority

## View Root Information

```text
show spanning-tree root
```

## Check a Specific Interface

```text
show spanning-tree interface GigabitEthernet0/1
```

## Check the STP Mode

```text
show spanning-tree summary
```

---

# 17. Troubleshooting RSTP

## Problem 1: Network Reconvergence Is Slow

Check:

- Is RSTP enabled?
- Is the switch running legacy STP?
- Are edge ports configured correctly?
- Are links operating as expected?
- Is an unexpected Root Bridge present?

## Problem 2: Unexpected Traffic Path

Run:

```text
show spanning-tree
```

Verify:

- Root Bridge
- Root Port
- Designated Ports
- Port roles
- Path cost
- Bridge priority

## Problem 3: An End-Device Port Causes a Topology Change

Possible causes:

- Another switch was connected accidentally.
- PortFast is missing or incorrectly configured.
- BPDU Guard is not configured.
- The port is being used for a purpose different from its intended edge role.

## RSTP Troubleshooting Flow

```text
Problem
   ↓
Check STP Mode
   ↓
Check Root Bridge
   ↓
Check Port Roles
   ↓
Check Port States
   ↓
Check Link Type / Edge Configuration
   ↓
Check BPDUs and Topology Changes
   ↓
Correct Configuration
   ↓
Verify Convergence
```

---

# 18. RSTP Best Practices

- Use RSTP instead of legacy STP where supported.
- Intentionally select the Root Bridge.
- Configure an appropriate secondary/root backup design.
- Enable PortFast only on end-device ports.
- Use BPDU Guard on appropriate access/edge ports.
- Monitor topology changes.
- Document bridge priorities.
- Prefer point-to-point full-duplex links between switches.
- Keep Layer 2 domains appropriately sized.
- Verify the actual forwarding topology after changes.

---

# 19. Common Interview Questions

### Q1. What is the main advantage of RSTP over STP?

RSTP significantly reduces convergence time while maintaining Layer 2 loop prevention.

### Q2. Which IEEE standard defines RSTP?

**IEEE 802.1w**

### Q3. What are the RSTP port roles?

- Root Port
- Designated Port
- Alternate Port
- Backup Port

### Q4. What are the RSTP port states?

- Discarding
- Learning
- Forwarding

### Q5. What is an Edge Port?

An Edge Port connects directly to an end device and can transition rapidly to Forwarding because it is not expected to create a Layer 2 loop.

### Q6. What is an Alternate Port?

An Alternate Port provides a backup path toward the Root Bridge and can become the Root Port when the active Root Port fails.

### Q7. What is a Backup Port?

A Backup Port provides redundancy for a Designated Port when multiple ports from the same switch connect to the same shared segment.

### Q8. Why does RSTP converge faster than traditional STP?

RSTP uses rapid port transitions, alternate/backup roles, edge-port behavior, and the Proposal/Agreement mechanism instead of relying solely on the traditional STP timer sequence.

### Q9. What is the difference between an Alternate Port and a Backup Port?

- **Alternate Port:** backup path toward the Root Bridge through another switch.
- **Backup Port:** redundant path to the same LAN segment through the same local switch.

### Q10. Should PortFast be enabled on a switch-to-switch link?

Normally, **no**. PortFast is intended for edge ports connected to end devices. Enabling it on an inappropriate switch-to-switch link can create a loop risk.

---

# 🎤 Interview Answer – Explain RSTP in 3–5 Minutes

> **"Rapid Spanning Tree Protocol, or RSTP, is an enhancement of the original Spanning Tree Protocol and is associated with IEEE 802.1w. Its primary purpose is still to prevent Layer 2 loops, but it provides much faster convergence when the network topology changes.**
>
> **RSTP retains the Root Bridge election process and the concepts of Root Ports and Designated Ports, but it adds Alternate and Backup port roles to represent redundant paths. It also simplifies the traditional five STP states into Discarding, Learning, and Forwarding.**
>
> **One of the most important RSTP improvements is the Proposal/Agreement mechanism. Instead of waiting through the traditional STP timer sequence, neighboring switches can rapidly agree that a port can safely transition toward forwarding. RSTP also treats end-device connections as Edge Ports, allowing them to move rapidly to forwarding, and it actively generates BPDUs from every switch.**
>
> **In an enterprise environment, RSTP is useful wherever Layer 2 redundancy is required. For example, if an access switch has two uplinks and the primary path fails, an Alternate Port can quickly become the Root Port and restore connectivity. This provides better availability for services such as voice, video, applications, and business systems.**
>
> **From a configuration perspective, Cisco networks can use Rapid PVST+ with 'spanning-tree mode rapid-pvst'. PortFast should be used on end-device ports, with BPDU Guard as an important protection mechanism. Verification is performed with commands such as 'show spanning-tree', 'show spanning-tree root', and interface-specific spanning-tree commands.**
>
> **In summary, RSTP provides the same fundamental Layer 2 loop-prevention goal as STP, but with faster convergence, additional port roles, simplified states, and mechanisms such as Proposal/Agreement that make redundant enterprise networks recover much more quickly."**

---

# 🧠 Memory Trick

Remember:

```text
RSTP = RAPID
```

### Roles

```text
R D A B
│ │ │ │
│ │ │ └── Backup
│ │ └──── Alternate
│ └────── Designated
└──────── Root
```

### States

```text
D L F
│ │ │
│ │ └── Forwarding
│ └──── Learning
└────── Discarding
```

### Convergence

```text
STP  → Slow
RSTP → Rapid
```

### Proposal/Agreement

```text
Proposal → Check → Agreement → Forward
```

---

# ⭐ Gold Interview Tip

If an interviewer asks:

> **"Explain RSTP in one sentence."**

Say:

> **"RSTP is the rapid version of STP that prevents Layer 2 loops while using faster convergence mechanisms, alternate paths, edge-port behavior, and Proposal/Agreement to restore connectivity quickly after failures."**

Then expand into:

**Root Bridge → Port Roles → Port States → Proposal/Agreement → Failure Recovery → Cisco Configuration.**

---

# 📝 Quick Revision Notes

| Topic | Remember |
|---|---|
| RSTP | Rapid Layer 2 loop prevention |
| Standard | IEEE 802.1w |
| Main Goal | Prevent Layer 2 loops |
| Convergence | Typically much faster than legacy STP |
| Roles | Root, Designated, Alternate, Backup |
| States | Discarding, Learning, Forwarding |
| Edge Port | End-device connection |
| Proposal/Agreement | Rapid transition mechanism |
| BPDU | Every switch actively generates BPDUs |
| Cisco Mode | `spanning-tree mode rapid-pvst` |
| Access Protection | PortFast + BPDU Guard where appropriate |

---

# 🎯 Assignment – Test Yourself

1. Compare STP and RSTP in terms of convergence, port roles, and port states.
2. Explain the IEEE 802.1w standard.
3. Draw a topology with one Root Bridge and identify Root, Designated, and Alternate Ports.
4. Explain the difference between Alternate and Backup Ports.
5. Explain how the Proposal/Agreement mechanism speeds up convergence.
6. Explain the three RSTP port states.
7. Explain the purpose of an Edge Port.
8. Explain how RSTP handles a Root Port failure.
9. Configure RSTP and PortFast on a Cisco switch in a lab or simulator.
10. Simulate a link failure in EVE-NG or Packet Tracer and observe the Alternate Port becoming active.
11. Use `show spanning-tree` to identify the Root Bridge and port roles.
12. Troubleshoot a scenario where an access port unexpectedly causes topology changes.
13. Explain why BPDU Guard should be used with appropriate PortFast/edge ports.
14. Give a 3–5 minute interview explanation of RSTP without looking at your notes.

---

# 🧪 Practical Assignment – Lab Practice

## Lab 1 – Basic RSTP Configuration

Build:

```text
SW1 -------- SW2
  \          /
   \        /
      SW3
```

Tasks:

1. Enable Rapid PVST+.
2. Configure bridge priorities.
3. Identify the Root Bridge.
4. Identify Root and Designated Ports.
5. Verify using:

```text
show spanning-tree
```

## Lab 2 – Alternate Path Failure

Build:

```text
          SW1
         /   \
       SW2   SW3
         \   /
          SW4
```

Tasks:

1. Identify the active forwarding path.
2. Identify the redundant/Alternate path.
3. Shut down a primary link.
4. Observe the RSTP role transition.
5. Verify the new forwarding path.

## Lab 3 – Edge Port + BPDU Guard

Configure:

```text
interface GigabitEthernet0/10
 spanning-tree portfast
 spanning-tree bpduguard enable
```

Tasks:

1. Connect a host.
2. Verify rapid forwarding.
3. Observe the interface behavior when an unexpected BPDU-producing device is connected.
4. Explain why this protects the access layer.

## Lab 4 – RSTP Troubleshooting

Create an intentional problem:

- Wrong Root Bridge priority
- Missing PortFast
- Incorrect edge-port configuration
- Unexpected redundant path

Use:

```text
show spanning-tree
show spanning-tree root
show spanning-tree interface GigabitEthernet0/1
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

You are designing the switching layer for a financial organization.

Requirements:

- Two distribution switches.
- Multiple access switches.
- Redundant uplinks.
- Fast recovery from a single link failure.
- Access ports must be protected from accidental switch connections.
- The Root Bridge must be intentionally selected.

Design:

1. Which switches should be Root and Secondary Root?
2. Where would you use PortFast?
3. Where would you use BPDU Guard?
4. How would you verify the RSTP topology?
5. How would you test failure recovery?
6. What evidence would you collect during troubleshooting?

---

# 📝 Chapter Summary

RSTP is the rapid evolution of traditional STP.

The most important concepts are:

```text
IEEE 802.1w
      ↓
Rapid Convergence
      ↓
Root Bridge
      ↓
Root / Designated / Alternate / Backup
      ↓
Discarding / Learning / Forwarding
      ↓
Proposal + Agreement
      ↓
Fast Failure Recovery
```

If you understand **why RSTP was created, how the Root Bridge is elected, what each port role means, how Proposal/Agreement works, and how an Alternate Port recovers from failure**, you understand the core of RSTP.

---

# 🚀 Next Chapter

## Chapter 11 – Multiple Spanning Tree Protocol (MSTP)

We'll learn how large enterprise networks with many VLANs can reduce spanning-tree overhead by mapping multiple VLANs to spanning-tree instances while maintaining loop-free Layer 2 topologies.
