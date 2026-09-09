# 📘 Module 1 – Networking Fundamentals

# Chapter 7 – Switching Deep Dive

---

# Learning Objectives

By the end of this chapter, you will understand:

- How a switch works internally
- MAC learning process
- VLANs
- Access vs Trunk Ports
- IEEE 802.1Q
- Native VLAN
- DTP
- VTP
- EtherChannel
- LACP
- PAgP
- VLAN Security
- Enterprise Design
- Troubleshooting

---

# 1. 7.1 How Does a Switch Work?

Imagine a company with 100 employees.
```
      PC1
        │
      PC2
        │
      PC3
        │
    Cisco Switch
        │
     FortiGate
```

When PC1 sends data:

1. Switch reads **Source MAC**
2. Learns it
3. Stores it in CAM/MAC table
4. Reads Destination MAC
5. Forwards only to correct port

Unlike a hub, a switch **does not flood every frame** once it has learned the MAC addresses.

---

# 2. CAM Table (MAC Address Table)

Example:

| MAC Address | VLAN | Port |
|---|---:|---|
| AA-AA-AA-AA-AA-AA | 10 | Gi0/1 |
| BB-BB-BB-BB-BB-BB | 10 | Gi0/2 |
| CC-CC-CC-CC-CC-CC | 20 | Gi0/10 |

Cisco Command
```
show mac address-table
```

---

# 3. Unknown Unicast

Suppose:

PC1 wants to reach PC4.

Switch has never seen PC4.

Result:
```
Unknown Destination

↓

Flood Frame

↓

All Ports (same VLAN only)

↓

PC4 Replies

↓

Switch Learns MAC
```

---

# 4. 7.2 What is a VLAN?

**VLAN = Virtual Local Area Network**

A VLAN logically separates devices into different broadcast domains, even when they are connected to the same physical switch.

---

## Without VLAN
```
PC1
PC2
PC3
Printer

↓

Same Broadcast Domain
```

Everyone receives broadcasts.

---

## With VLAN
```
VLAN10 (HR)

PC1
PC2

------------------

VLAN20 (Finance)

PC3
PC4
```

HR broadcasts stay within VLAN 10.

Finance broadcasts stay within VLAN 20.

---

# 5. Why VLANs?

Suppose a company has:

- HR
- Finance
- IT
- Guest Wi-Fi

You don't want Guest Wi-Fi users directly accessing Finance systems.

Each department gets its own VLAN.

Benefits:

- Security
- Better broadcast control
- Easier management
- Improved performance

---

# 6. Real Enterprise Example

| Department | VLAN |
|---|---:|
| HR | 10 |
| Finance | 20 |
| IT | 30 |
| Servers | 50 |
| Guest Wi-Fi | 100 |
| Management | 999 |

---

# 7. 7.3 Broadcast Domain

Without VLANs:
```
100 PCs

↓

One Broadcast Domain
```

One ARP request reaches all 100 devices.

---

With VLANs:
```
VLAN10

30 PCs

VLAN20

40 PCs

VLAN30

30 PCs
```

Broadcasts stay within each VLAN.

---

# 8. 7.4 Access Port

An Access Port belongs to **one VLAN only**.

Example:
```
PC

↓

Switch Port

↓

VLAN10
```

Frames are **untagged**.

Cisco Configuration
```
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
```

---

# 9. 7.5 Trunk Port

A Trunk Port carries **multiple VLANs** between network devices.

Example:
```
Switch A

||

Trunk

||

Switch B
```

Carries:

- VLAN10
- VLAN20
- VLAN30

---

# 10. 7.6 IEEE 802.1Q (VLAN Tagging)

Entity: IEEE 802.1Q

This is **not encryption**.

It adds a **4-byte VLAN tag** to Ethernet frames.

Example:

Without tag:
```
Ethernet Header

↓

Data
```

With tag:
```
Ethernet Header

↓

802.1Q Tag

↓

Data
```

Tag contains:

- VLAN ID
- Priority (802.1p)
- CFI/DEI bit

---

# 11. VLAN IDs

| VLAN | Purpose |
|---:|---|
| 1 | Default VLAN (avoid for users) |
| 10 | HR |
| 20 | Finance |
| 30 | IT |
| 999 | Native/Unused VLAN (common practice) |

Valid VLAN range:

- Normal: **1–1005**
- Extended: **1006–4094**

---

# 12. 7.7 Native VLAN

The Native VLAN is the VLAN whose frames are sent **without an 802.1Q tag** on a trunk.

Example:

Native VLAN:
```
999
```

Traffic in VLAN 999 crosses the trunk untagged.

Best Practice:

- Never use VLAN 1 as the Native VLAN.
- Use an unused VLAN (e.g., 999).
- Do not assign user devices to the Native VLAN.

---

# 13. 7.8 Inter-VLAN Routing

Question:

Can VLAN10 communicate with VLAN20?

No.

A Layer 2 switch cannot route between VLANs.

Need:

- Router
- Layer 3 Switch
- Firewall

Example:
```
HR VLAN10

↓

FortiGate

↓

Finance VLAN20
```

Firewall can also apply security policies between VLANs.

---

# 14. 7.9 DTP (Dynamic Trunking Protocol)

Entity: Dynamic Trunking Protocol

Cisco proprietary.

Purpose:

Automatically negotiates whether a link should become a trunk.

Modes:

- Dynamic Auto
- Dynamic Desirable
- Trunk
- Access
- Nonegotiate

Best Practice:

Disable DTP on production trunk links.
```
switchport nonegotiate
```

---

# 15. 7.10 VTP (VLAN Trunking Protocol)

Entity: VLAN Trunking Protocol

Purpose:

Distributes VLAN configuration between Cisco switches.

Modes:

- Server
- Client
- Transparent

---

## Risk

A switch with a higher VTP revision number can overwrite VLAN information across the domain.

Best Practice:

Many enterprises use **Transparent Mode** or avoid VTP unless centralized VLAN management is required.

---

# 16. 7.11 EtherChannel

Problem:

One uplink becomes congested.

Solution:

Bundle multiple physical links into one logical link.
```
Switch A

=== Cable 1 ===

=== Cable 2 ===

=== Cable 3 ===

↓

EtherChannel
```

Benefits:

- Higher bandwidth
- Redundancy
- No STP blocking on individual bundled links

---

# 17. 7.12 LACP vs PAgP

### LACP

Entity: Link Aggregation Control Protocol

Open Standard (IEEE 802.3ad / 802.1AX)

Modes:

- Active
- Passive

Supported by multiple vendors.

---

### PAgP

Entity: Port Aggregation Protocol

Cisco Proprietary

Modes:

- Desirable
- Auto

Used only between Cisco devices.

---

# 18. Comparison

| Feature | LACP | PAgP |
|---|---|---|
| Standard | IEEE | Cisco |
| Multi-vendor | ✅ | ❌ |
| Preferred Today | ✅ | Legacy Cisco |

---

# 19. 7.13 VLAN Hopping Attack

Attackers try to access traffic from another VLAN.

Methods:

- Switch Spoofing
- Double Tagging

---

# 20. Protection

- Disable DTP
- Manually configure trunk ports
- Change Native VLAN
- Do not use VLAN 1
- Disable unused ports
- Restrict allowed VLANs on trunks

Example:
```
switchport trunk allowed vlan 10,20,30
```

---

# 21. 7.14 Switch Security Best Practices

- Disable unused ports
- Enable Port Security
- Use BPDU Guard
- Use Root Guard
- Enable DHCP Snooping
- Enable Dynamic ARP Inspection
- Enable Storm Control
- Use SSH instead of Telnet
- Use SNMPv3
- Disable DTP
- Restrict trunk VLANs
- Avoid VLAN 1
- Use AAA (RADIUS/TACACS+)

---

# 22. Real Enterprise Design
```
                    Internet
                        │
                  FortiGate HA
                        │
                 Core L3 Switch
                 ╱            ╲
         Distribution      Distribution
             ╱                  ╲
       Access Switch      Access Switch
          │                    │
 HR VLAN10 PCs          Finance VLAN20 PCs
 IT VLAN30 PCs          Guest VLAN100
```

Inter-VLAN traffic passes through the Layer 3 switch or firewall, where security policies can be enforced.

---

# 23. Troubleshooting Scenario 1

### Problem

HR users cannot access Finance.

### Investigation

1. Check VLAN assignment.
2. Verify trunk allows VLAN 10 and 20.
3. Check Inter-VLAN routing.
4. Verify firewall policies.
5. Test with `ping` and `traceroute`.

---

# 24. Troubleshooting Scenario 2

### Problem

New switch added.

Users lose VLANs.

### Investigation

Check:
```
show vtp status
```

Possible Cause:

New switch had a higher VTP revision number and overwrote the VLAN database.

---

# 25. Troubleshooting Scenario 3

### Problem

Switches cannot communicate over VLAN 20.

Check:
```
show interfaces trunk
```

Verify VLAN 20 is allowed on the trunk.

---

# 26. Common Interview Questions

### Q1. What is the difference between an Access Port and a Trunk Port?

| Access | Trunk |
|---|---|
| One VLAN | Multiple VLANs |
| Untagged frames | Tagged frames (except Native VLAN) |

---

### Q2. Is 802.1Q encryption?

**Answer:** No. It only adds a VLAN tag to identify the VLAN. It does not encrypt traffic.

---

### Q3. Why shouldn't VLAN 1 be used for users?

Because it's the default VLAN on many switches and is commonly targeted in attacks. Using separate VLANs for users and changing the native VLAN reduces risk.

---

### Q4. Why is LACP preferred over PAgP?

Because LACP is an IEEE open standard and works in multi-vendor environments.

---

### Q5. Why do we need VLANs?

To:

- Separate departments
- Improve security
- Reduce broadcast domains
- Simplify network management

---

# 🎯 Interview Answer (4–5 Minutes)

> "A switch operates at Layer 2 and forwards Ethernet frames based on MAC addresses stored in its CAM table. VLANs logically segment a physical switch into multiple broadcast domains, improving security and reducing unnecessary broadcast traffic. Access ports connect end devices and carry traffic for a single VLAN, while trunk ports carry traffic for multiple VLANs using IEEE 802.1Q tagging. The native VLAN carries untagged traffic and should be changed from VLAN 1 as a security best practice. Inter-VLAN communication requires a Layer 3 device such as a router, Layer 3 switch, or firewall. In Cisco environments, DTP can negotiate trunk links automatically, but it is typically disabled for security. VTP distributes VLAN information but must be used carefully because incorrect revision numbers can overwrite VLAN databases. EtherChannel combines multiple physical links into one logical link for redundancy and higher bandwidth, with LACP being the preferred open-standard protocol."

---

# 📝 Chapter 7 Quick Revision

✅ Switch learns MAC addresses from the **source MAC**.  
✅ CAM Table = **MAC → Port**.  
✅ VLAN = Separate **broadcast domain**.  
✅ Access Port = **One VLAN**.  
✅ Trunk Port = **Multiple VLANs**.  
✅ 802.1Q = **VLAN tagging**, not encryption.  
✅ Native VLAN = **Untagged traffic** on a trunk.  
✅ Inter-VLAN Routing requires a **Layer 3 device**.  
✅ Disable **DTP** in production.  
✅ Use **Transparent Mode** or carefully manage **VTP**.  
✅ Prefer **LACP** over **PAgP**.  
✅ Protect against VLAN hopping by changing the native VLAN, disabling DTP, and limiting allowed VLANs.

---


---

# 🎯 Assignment – Test Yourself

Try answering these without looking back at the chapter.

### Question 1
A switch receives a frame with:

- Source MAC: AA-AA-AA-AA-AA-AA
- Destination MAC: BB-BB-BB-BB-BB-BB

What does the switch learn from the frame?

### Question 2
What happens when the destination MAC address is not present in the CAM table?

### Question 3
What is the difference between a broadcast domain and a VLAN?

### Question 4
A PC is connected to VLAN 10. Should the port connecting the PC normally be configured as an access port or a trunk port?

### Question 5
A link between two switches needs to carry VLANs 10, 20, and 30. Should it be an access port or a trunk port?

### Question 6
What is the purpose of the IEEE 802.1Q tag?

### Question 7
What is the Native VLAN, and why is it normally changed from VLAN 1?

### Question 8
Can a Layer 2 switch route traffic directly from VLAN 10 to VLAN 20? What device or feature is required?

### Question 9
What is DTP? Why might an administrator disable it on production links?

### Question 10
What is VTP? What is the danger of introducing a switch with an unexpected VTP revision number?

### Question 11
What problem does EtherChannel solve?

### Question 12
Compare LACP and PAgP. Which one is preferred in a multi-vendor environment and why?

### Question 13
Name two common VLAN-hopping techniques.

### Question 14
Give at least five switch-security best practices.

### Question 15 – Troubleshooting Scenario

Users in HR VLAN 10 cannot access Finance VLAN 20.

Build a troubleshooting sequence using:

```text
VLAN assignment
      ↓
Trunk
      ↓
Inter-VLAN routing
      ↓
Firewall / ACL policy
      ↓
Connectivity testing
```

Write the commands you would use where appropriate.

---

# 🧪 Practical Assignment – Lab Practice

## Lab 1 – MAC Learning

Create a small topology:

```text
PC1 ───── Switch ───── PC2
```

Tasks:

1. Clear or inspect the switch MAC table.
2. Generate traffic from PC1 to PC2.
3. Check the MAC table.
4. Identify which MAC address was learned on which port.

Useful command:

```text
show mac address-table
```

---

## Lab 2 – VLAN Configuration

Create:

```text
VLAN 10 = HR
VLAN 20 = Finance
VLAN 30 = IT
```

Assign:

```text
Gi0/1 → VLAN 10
Gi0/2 → VLAN 20
Gi0/3 → VLAN 30
```

Verify the VLAN assignments and test communication between hosts in the same VLAN.

---

## Lab 3 – Trunk Configuration

Create:

```text
Switch A
   ||
 Trunk
   ||
Switch B
```

Allow:

```text
VLAN 10
VLAN 20
VLAN 30
```

Verify:

```text
show interfaces trunk
```

Confirm that the required VLANs are allowed on the trunk.

---

## Lab 4 – Inter-VLAN Routing

Build:

```text
          Layer 3 Device / Firewall
                 /       \
             VLAN 10    VLAN 20
                |          |
               HR       Finance
```

Tasks:

1. Configure VLAN interfaces.
2. Configure IP addressing.
3. Verify the default gateways.
4. Test VLAN 10 → VLAN 20 communication.
5. If a firewall is used, create and test the required security policy.
6. Use `ping` and `traceroute` to verify the path.

---

## Lab 5 – VLAN Troubleshooting

Intentionally create one problem at a time:

- Put a PC in the wrong VLAN.
- Remove VLAN 20 from the trunk allowed list.
- Configure an incorrect gateway.
- Block inter-VLAN traffic with a firewall policy.

Then troubleshoot from Layer 2 → Layer 3 → Security.

---

## Lab 6 – EtherChannel

Build:

```text
Switch A
 ║ ║ ║
 ║ ║ ║
Switch B
```

Configure multiple physical links as one EtherChannel using LACP.

Verify:

```text
show etherchannel summary
```

Confirm that the member interfaces are bundled correctly.

---

## Lab 7 – Switch Security Review

Create a checklist for a production switch:

```text
[ ] Unused ports disabled
[ ] Port security considered
[ ] BPDU Guard enabled where appropriate
[ ] DHCP Snooping enabled where appropriate
[ ] Dynamic ARP Inspection considered
[ ] Storm Control considered
[ ] SSH used instead of Telnet
[ ] DTP disabled where appropriate
[ ] Trunk VLANs restricted
[ ] Native VLAN not used for normal user traffic
[ ] AAA configured
```

Explain **why** each control is useful.

---

# 🧠 Assignment Challenge

Design a small enterprise network for:

```text
HR          → VLAN 10
Finance     → VLAN 20
IT          → VLAN 30
Servers     → VLAN 50
Guest Wi-Fi → VLAN 100
Management  → VLAN 999
```

Your design should include:

- Access ports
- Trunk ports
- Inter-VLAN routing
- Firewall policy considerations
- Native VLAN
- Allowed VLANs
- EtherChannel
- Basic switch security

Draw the topology using ASCII and explain the traffic path when:

```text
HR PC → Finance Server
Guest PC → Internet
IT PC → Management VLAN
```

---


# 🚀 Next Chapter

**Chapter 8 – Spanning Tree Protocol (STP) Deep Dive**

We'll cover:

- Why loops occur
- Broadcast storms
- MAC table instability
- STP algorithm
- Root Bridge election
- Root Port & Designated Port selection
- Port states
- Port roles
- RSTP vs STP vs MSTP
- BPDU
- BPDU Guard
- Root Guard
- Loop Guard
- PortFast
- STP troubleshooting with real enterprise scenarios

This is another **10/10 interview topic**, especially for Cisco, Fortinet, and enterprise networking roles.
