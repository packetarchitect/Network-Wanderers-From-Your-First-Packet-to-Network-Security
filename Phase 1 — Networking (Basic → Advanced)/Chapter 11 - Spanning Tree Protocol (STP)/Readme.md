# 📘 Module 1 – Networking Fundamentals

# Chapter 11 – Spanning Tree Protocol (STP) – Complete Deep Dive

---

# Learning Objectives

By the end of this chapter, you will understand:

- Why Layer 2 loops occur
- Broadcast storms
- MAC table instability
- STP purpose
- STP algorithm
- BPDU
- Root Bridge election
- Bridge ID
- Root Port
- Designated Port
- Blocking Port
- STP path cost
- Port states
- STP timers
- Convergence
- Enterprise STP design
- STP troubleshooting
- Interview scenarios

---

# 1. What is STP?

**STP (Spanning Tree Protocol)** is a **Layer 2 protocol** that prevents **switching loops** in Ethernet networks by creating a loop-free logical topology.

Entity: Spanning Tree Protocol

IEEE Standard:
```text
IEEE 802.1D
```

STP does not remove physical redundancy.

Instead, it logically blocks redundant paths while keeping them available for failover.

---

# 2. Why Do We Need STP?

Suppose you have two switches connected by **two cables**.

```text
        Switch A
       /        \
      /          \
     /            \
 Switch B----------
```

Looks great, right?

Actually...

❌ It's dangerous.

Because Ethernet frames **do not have a Time To Live (TTL)** like IP packets.

If a frame enters a loop:

```text
A → B → A → B → A → B → ...
```

It never stops.

This creates a **Layer 2 loop**.

---

# 3. What Happens Without STP?

Three major problems occur.

---

## 3.1 Broadcast Storm ⭐

One ARP request can become:

```text
ARP

↓

Switch A

↓

Switch B

↓

Switch A

↓

Switch B

↓

Forever
```

Eventually:

- Network bandwidth = 100%
- CPU utilization increases
- Users lose connectivity

---

## 3.2 MAC Address Table Instability

Suppose PC1 is connected to Switch A.

Because frames keep looping:

Switch B learns:

```text
MAC
↓
Port 1
```

A moment later:

```text
Same MAC
↓
Port 2
```

Then:

```text
Port 1
↓
Port 2
↓
Port 1
↓
Port 2
```

The MAC address table constantly changes.

This is called:

> **MAC Address Flapping**

---

## 3.3 Duplicate Frames

A server may receive the same Ethernet frame multiple times.

Example:

```text
Order Request

↓

Server receives it twice
```

This can cause:

- Duplicate transactions
- Application errors
- Unpredictable behavior

---

# 4. Real Banking Example

Imagine:

```text
ATM Network
      ↓
Core Switch
      ↓
Distribution Switch
      ↓
Access Switch
```

A technician accidentally creates a loop.

Result:

- ATM transactions fail
- Online banking slows down
- Core switches hit high CPU
- Network outage

This is exactly why STP is enabled by default on most enterprise switches.

---

# 5. How STP Works

STP creates a **logical loop-free topology**.

Instead of removing redundant links:

It blocks one.

Example:

```text
        Switch A
       /        \
      /          X (Blocked)
     /
 Switch B
```

Physical redundancy remains.

Logical loop is removed.

If the active link fails:

```text
Active Link Fails
        ↓
STP Recalculates
        ↓
Blocked Link Can Become Forwarding
        ↓
Connectivity Restored
```

So STP provides:

**Redundancy + Loop Prevention**

---

# 6. STP Goals

- Prevent loops
- Maintain redundancy
- Automatically recover after failures

---

# 7. STP Terminology

| Term | Meaning |
|---|---|
| Root Bridge | The central switch of the STP topology |
| BPDU | Bridge Protocol Data Unit |
| Root Port | Best path toward the Root Bridge |
| Designated Port | Forwarding port for a network segment |
| Blocking Port | Prevents loops |
| Path Cost | Cost used to select the best path |

---

# 8. Root Bridge Election

The first step in STP is electing the **Root Bridge**.

Only **one Root Bridge** exists per STP instance.

Election is based on the **Bridge ID (BID)**.

---

## Bridge ID (BID)

A Bridge ID consists of:

```text
Priority
+
MAC Address
```

Example:

```text
Priority

32768

+

MAC

00:11:22:33:44:55
```

---

## Election Rule

**Lowest Bridge ID wins.**

Priority is compared first.

If priorities are equal:

Lowest MAC address wins.

---

## Example

| Switch | Priority | MAC |
|---|---:|---|
| SW1 | 32768 | 00:11:22:33:44:01 |
| SW2 | 32768 | 00:11:22:33:44:02 |
| SW3 | 4096 | 00:11:22:33:44:FF |

Winner:

✅ SW3

Even though its MAC is higher, its priority is lower.

---

## Best Practice

Don't let STP choose the Root Bridge randomly.

Configure it manually.

Example:

```text
spanning-tree vlan 10 priority 4096
```

---

# 9. BPDU (Bridge Protocol Data Unit)

Switches exchange special STP messages called **BPDUs**.

Purpose:

- Elect Root Bridge
- Detect topology changes
- Maintain STP topology

Think of BPDU as:

> "Hello, I'm Switch A. My Bridge ID is X."

---

# 10. Port Roles

## 10.1 Root Port (RP)

Every non-root switch has **one Root Port**.

Definition:

> Port with the **lowest path cost** to the Root Bridge.

Always:

**Forwarding**

---

## 10.2 Designated Port (DP)

Each network segment has **one Designated Port**.

Responsible for forwarding traffic for that segment.

Always:

**Forwarding**

---

## 10.3 Blocking Port

Any extra path that could create a loop becomes:

**Blocking**

It:

- Does not forward user traffic
- Still listens for BPDUs

---

# 11. Example of Port Roles

```text
         Root Bridge
           SW1
          /   \
         /     \
      SW2------SW3
```

Possible roles:

```text
SW1

↓

Designated Ports

SW2

↓

Root Port

SW3

↓

Root Port

One redundant port

↓

Blocking
```

---

# 12. Classic STP Port States

Ports transition through these states:

| State | Forwards Data? | Learns MAC? |
|---|---|---|
| Disabled | ❌ | ❌ |
| Blocking | ❌ | ❌ |
| Listening | ❌ | ❌ |
| Learning | ❌ | ✅ |
| Forwarding | ✅ | ✅ |

---

## Why So Many States?

To prevent temporary loops while the topology stabilizes.

Default convergence time:

Around **30–50 seconds**.

---

# 13. STP Path Cost

If multiple paths exist, STP chooses the path with the **lowest total cost**.

Historically:

| Speed | Cost |
|---|---:|
| 10 Mbps | 100 |
| 100 Mbps | 19 |
| 1 Gbps | 4 |
| 10 Gbps | 2 |

Lower cost is preferred.

---

# 14. Topology Change

Suppose:

```text
SW1
 │
SW2
 │
PC
```

The link between SW1 and SW2 fails.

STP detects:

- BPDU loss
- Topology change

Previously blocked ports may transition to forwarding.

Connectivity is restored without manual intervention.

---

# 15. Packet Flow Example

Before STP:

```text
PC

↓

SW1

↓

SW2

↓

SW3

↓

SW1

↓

Infinite Loop
```

After STP:

```text
PC

↓

SW1

↓

SW2

↓

Blocked Link

❌

No Loop
```

---

# 16. Enterprise STP Design

```text
             Core
          /        \
         /          \
 Distribution1   Distribution2
       |             |
       |             |
   Access SW     Access SW
```

STP blocks only the necessary redundant links while preserving failover paths.

---

# 17. Troubleshooting Scenario 1

### Problem

Entire office network is slow.

Symptoms:

- High switch CPU
- MAC flapping
- Broadcast traffic spike

Check:

```text
show spanning-tree
```

Also verify:

- Blocking ports
- Topology changes
- Root Bridge

---

# 18. Troubleshooting Scenario 2

### Problem

Wrong Root Bridge elected.

Solution:

Configure the intended Core Switch:

```text
spanning-tree vlan 10 priority 4096
```

---

# 19. Troubleshooting Scenario 3

### Problem

User plugged two wall ports into the same unmanaged switch.

Result:

Loop

STP blocks one path if the connected equipment participates in STP.

If unmanaged equipment ignores STP:

Network outage may occur.

---

# 20. Common Interview Questions

## Q1. Why is STP required?

To prevent Layer 2 loops while maintaining redundant links.

---

## Q2. What problems occur without STP?

- Broadcast Storm
- MAC Address Flapping
- Duplicate Frames

---

## Q3. How is the Root Bridge selected?

The switch with the **lowest Bridge ID** (Priority + MAC Address).

---

## Q4. What is a BPDU?

A Bridge Protocol Data Unit used by switches to exchange STP information and maintain a loop-free topology.

---

## Q5. Why is one port blocked?

To eliminate redundant Layer 2 paths that could otherwise create switching loops.

---

# Practical Cisco Commands

### Check STP

```text
show spanning-tree
```

---

### Check Root Bridge

```text
show spanning-tree root
```

---

### View Interface Role

```text
show spanning-tree interface GigabitEthernet0/1
```

---

# Interview Answer (5 Minutes)

> "Spanning Tree Protocol, or STP, is a Layer 2 protocol defined by IEEE 802.1D that prevents switching loops in Ethernet networks. It works by electing a single Root Bridge based on the lowest Bridge ID, which consists of the bridge priority and MAC address. Switches exchange BPDUs to discover the network topology and determine the best paths. Each non-root switch selects one Root Port toward the Root Bridge, each network segment has one Designated Port, and any redundant ports are placed into the Blocking state to eliminate loops. If a link fails, STP recalculates the topology and activates a previously blocked redundant path, providing high availability while preventing broadcast storms, MAC address table instability, and duplicate frames."

---

# 📝 Chapter 9 Quick Revision

✅ STP = **IEEE 802.1D**

✅ Purpose = **Prevent Layer 2 loops**

✅ Problems Without STP:

- Broadcast Storm
- MAC Flapping
- Duplicate Frames

✅ Root Bridge = **Lowest Bridge ID**

✅ Bridge ID = **Priority + MAC Address**

✅ Root Port = **Best path to Root Bridge**

✅ Designated Port = **Forwards traffic for a segment**

✅ Blocking Port = **Prevents loops**

✅ BPDUs = **Used to elect the Root Bridge and maintain the topology**

---

# 🎯 Assignment – Test Yourself

Try answering these without looking back at the chapter.

### Question 1
What problem does STP solve?

### Question 2
Why can Ethernet Layer 2 frames create a loop indefinitely?

### Question 3
Name the three major problems that can occur without STP.

### Question 4
What is a Broadcast Storm?

### Question 5
What is MAC Address Flapping?

### Question 6
What is the purpose of the Root Bridge?

### Question 7
What makes up the Bridge ID?

### Question 8
If two switches have the same STP priority, how is the winner selected?

### Question 9
What is a Root Port?

### Question 10
What is a Designated Port?

### Question 11
Why is a redundant port placed into the Blocking state?

### Question 12
What is a BPDU?

### Question 13
What are the five classic STP port states?

### Question 14
Which path does STP prefer when multiple paths exist?

### Question 15
A network suddenly experiences high CPU, MAC flapping, and broadcast traffic. What would you investigate first?

### Question 16
A wrong switch became the Root Bridge. What configuration can you change to influence the election?

### Question 17 – Interview Challenge

Explain this topology:

```text
        SW1
       /   \
      /     \
    SW2-----SW3
```

Identify:

- Possible Root Bridge
- Root Ports
- Designated Ports
- Which redundant path could be blocked

---

# 🧪 Practical Assignment – Lab Practice

## Lab 1 – Observe the STP Topology

Build:

```text
SW1 -------- SW2
 |            |
 |            |
 └---- SW3 ---┘
```

Tasks:

1. Configure the switches.
2. Create a Layer 2 VLAN.
3. Connect redundant links.
4. Check the STP topology.
5. Identify the Root Bridge.
6. Identify Root Ports.
7. Identify Designated Ports.
8. Identify the blocked/alternate path.

Useful command:

```text
show spanning-tree
```

---

## Lab 2 – Change the Root Bridge

Choose SW1 as the intended Root Bridge.

Use an appropriate STP priority:

```text
spanning-tree vlan 10 priority 4096
```

Verify:

```text
show spanning-tree root
```

Confirm that SW1 becomes the Root Bridge.

---

## Lab 3 – Observe a Failover

Create:

```text
       SW1
      /   \
     /     \
   SW2-----SW3
```

1. Identify the blocked redundant path.
2. Disconnect the active forwarding path.
3. Observe the STP topology.
4. Check whether the previously blocked path becomes forwarding.
5. Test connectivity.

Record:

```text
Before Failure
      ↓
Which Port Was Blocked?
      ↓
Link Failure
      ↓
STP Recalculation
      ↓
New Forwarding Path
```

---

## Lab 4 – Troubleshoot a Loop

Simulate a switching-loop scenario in a controlled lab.

Observe:

- CPU utilization
- Broadcast traffic
- MAC address-table changes
- STP topology

Use:

```text
show spanning-tree
show mac address-table
```

Document:

```text
Symptom
   ↓
Evidence
   ↓
Likely Loop
   ↓
STP Investigation
   ↓
Root Cause
   ↓
Fix
```

---

## Lab 5 – STP Interview Drill

Without looking at the chapter, explain:

```text
Root Bridge
      ↓
Root Port
      ↓
Designated Port
      ↓
Blocking Port
      ↓
BPDU
      ↓
Path Cost
      ↓
Failover
```

Try to explain the complete STP process in **3 minutes**.

---

# 🚀 Next Chapter

## **Chapter 12 – Rapid Spanning Tree Protocol (RSTP)**

We'll cover:

- Why classic STP is slow
- IEEE 802.1w
- Rapid convergence
- New port roles and states
- Proposal/Agreement mechanism
- Edge ports
- Link types
- Comparison: STP vs RSTP vs MSTP
- Real enterprise deployment
- Troubleshooting scenarios
- Interview questions

RSTP is the protocol most modern enterprise switches run today, so understanding how it improves upon STP is essential for networking and firewall interviews.
