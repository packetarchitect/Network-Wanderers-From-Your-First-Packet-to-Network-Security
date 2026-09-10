# 📘 Module 2 – Switching

# Chapter 9 – VLAN Fundamentals (Basic to Advanced)

---

# 📚 What This Chapter Covers

- What is a VLAN?
- Why VLANs are Required
- History of VLANs
- Physical LAN vs Logical LAN
- Broadcast Domain
- Collision Domain
- Types of VLANs
- VLAN IDs & Reserved VLANs
- Default VLAN
- Native VLAN (Introduction)
- Management VLAN
- Voice VLAN
- Private VLAN (PVLAN)
- Data VLAN
- Guest VLAN
- Dynamic VLAN
- VLAN Membership
- Static vs Dynamic VLAN Assignment
- VLAN Database
- VLAN Communication
- Inter-VLAN Communication (Introduction)
- Enterprise VLAN Design
- VLAN Best Practices
- VLAN Security Basics
- Real-World Enterprise Use Cases
- Common VLAN Misconfigurations
- Troubleshooting VLAN Issues
- Interview Questions & Answers
- Hands-on Lab
- Key Takeaways
- Quick Revision Notes

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain what a VLAN is and why organizations use it.
- Differentiate between physical and logical network segmentation.
- Understand broadcast and collision domains.
- Identify different types of VLANs and their purposes.
- Design VLANs for enterprise environments.
- Explain how VLANs improve security and network performance.
- Troubleshoot common VLAN issues.
- Confidently answer VLAN interview questions.

---

# 1. What is a VLAN?

**VLAN** stands for:

> **Virtual Local Area Network**

A VLAN is a **logical segmentation** of a Layer 2 network that creates separate **broadcast domains** on the same physical switch infrastructure.

### Interview Definition

> **A VLAN is a logical method of dividing a physical switch into multiple independent broadcast domains, allowing devices to communicate as if they were on separate physical switches.**

---

# Why Was VLAN Introduced?

Imagine a company with 200 employees connected to one switch.

```text
                  One Switch
        ┌────────────────────────┐
 HR PC  │                        │ Finance PC
 IT PC  │                        │ Server
 Guest  │                        │ Printer
        └────────────────────────┘
```

Without VLANs:

- Everyone belongs to the same network.
- Every ARP broadcast reaches everyone.
- Any user can potentially access devices from other departments (unless restricted elsewhere).
- Large broadcast domains reduce efficiency.

This is not secure or scalable.

---

# VLAN Solution

Now divide the switch logically.

```text
                  One Physical Switch

 ┌────────────────────────────────────────┐
 │ VLAN 10 (HR)      → HR PCs             │
 │ VLAN 20 (Finance) → Finance PCs        │
 │ VLAN 30 (IT)      → IT Team            │
 │ VLAN 40 (Servers) → Application Servers│
 │ VLAN100 (Guest)   → Guest Wi-Fi        │
 └────────────────────────────────────────┘
```

Although all devices are connected to the same physical switch, they behave as if they are on **different switches**.

---

# Real-Life Enterprise Example

Imagine a bank.

```text
                    Core Switch
                         │
      ┌──────────────────┼──────────────────┐
      │                  │                  │
 VLAN10             VLAN20             VLAN30
 Human Resources    Finance            IT
      │                  │                  │
 HR PCs            Finance PCs        IT PCs
```

Benefits:

- HR cannot directly communicate with Finance.
- Finance cannot directly access IT.
- Traffic is isolated.
- Firewall policies can control communication between departments.

---

# 2. Physical LAN vs Logical LAN

## Physical LAN

Without VLANs:

```text
PC1
PC2
PC3
PC4

↓

Same Switch

↓

Same Network
```

Everyone shares one broadcast domain.

---

## Logical LAN (VLAN)

```text
          One Switch

 VLAN10        VLAN20

PC1             PC3
PC2             PC4
```

One physical switch behaves like multiple independent logical switches.

---

# 3. Why Do Organizations Use VLANs?

## 3.1 Security

Finance should not directly communicate with Guest Wi-Fi.

```text
Guest VLAN

↓

Cannot Access

↓

Finance VLAN
```

The firewall enforces access between VLANs.

## 3.2 Performance

Without VLANs, an ARP request may reach all 500 devices.

With VLANs, an ARP request from VLAN 10 stays within VLAN 10.

This reduces unnecessary broadcast traffic.

## 3.3 Easier Management

Instead of buying separate switches for every department:

One switch.

Multiple VLANs.

Lower cost.

## 3.4 Flexibility

If an employee moves desks, no rewiring is required. Configure the new switch port for the correct VLAN.

---

# 4. Broadcast Domain

A broadcast domain is the group of devices that receive Layer 2 broadcast traffic.

Example:

> "Who has 192.168.10.1?"

Without VLANs:

```text
100 PCs receive the ARP.
```

With VLANs:

```text
Only devices in VLAN 10 receive it.
```

Each VLAN creates a **separate broadcast domain**.

---

# 5. Collision Domain

A collision domain is the portion of the network where Ethernet frame collisions can occur.

### Hub

```text
PC1
 \
  Hub
 /
PC2
```

One collision domain.

### Switch

```text
PC1 → Port1

PC2 → Port2
```

Each switch port is its own collision domain.

Modern switched Ethernet operating in full-duplex mode effectively eliminates collisions during normal operation.

---

# Broadcast Domain vs Collision Domain

| Feature | Broadcast Domain | Collision Domain |
|---|---|---|
| Layer | Layer 2 | Layer 1/2 |
| Created By | VLAN or Router | Switch Port |
| Purpose | Controls broadcasts | Separates Ethernet collisions |
| Reduced By | VLANs/Routers | Switches |

---

# 6. VLAN IDs

Every VLAN has a unique numeric identifier.

| VLAN ID | Department |
|---:|---|
| 10 | HR |
| 20 | Finance |
| 30 | IT |
| 40 | Servers |
| 100 | Guest Wi-Fi |
| 999 | Native/Unused (common practice) |

## Valid VLAN Range

| Range | Purpose |
|---|---|
| 1 | Default VLAN |
| 2–1001 | Normal Range |
| 1002–1005 | Reserved (legacy technologies) |
| 1006–4094 | Extended Range |

---

# 7. Types of VLANs

## 7.1 Data VLAN

Carries normal user traffic.

Examples:

- PCs
- Laptops

## 7.2 Voice VLAN

Dedicated for IP phones.

```text
Cisco IP Phone

↓

Voice VLAN 50
```

Benefits:

- QoS prioritization
- Better voice quality

## 7.3 Management VLAN

Used for managing infrastructure devices.

Examples:

- Switch management
- Access Points
- Firewalls
- Controllers

Best practice:

Never use VLAN 1 for management.

## 7.4 Native VLAN

The Native VLAN carries **untagged traffic** on a trunk link.

Detailed implementation is covered in the next chapter.

## 7.5 Guest VLAN

Used for visitors.

```text
Guest
  ↓
Internet
  ↓
No Internal Access
```

## 7.6 Private VLAN (PVLAN)

Used mainly in:

- Data Centers
- Hosting Providers
- Cloud environments

Allows devices in the same subnet to be isolated from each other while still communicating with a gateway.

## 7.7 Dynamic VLAN

Users can be placed into VLANs based on identity or policy, often using authentication such as IEEE 802.1X with RADIUS.

---

# 8. VLAN Membership

## Static VLAN

Administrator manually assigns a switch port.

```text
Gi0/1
  ↓
VLAN10
```

Most common in enterprises.

## Dynamic VLAN

The switch assigns the VLAN automatically after successful authentication or policy evaluation.

```text
Employee logs in.
      ↓
RADIUS authenticates.
      ↓
Assigned to VLAN 20.
```

---

# 9. Enterprise VLAN Design

| VLAN | Purpose |
|---:|---|
| 10 | HR |
| 20 | Finance |
| 30 | IT |
| 40 | Servers |
| 50 | Voice |
| 60 | CCTV |
| 70 | Printers |
| 80 | IoT |
| 100 | Guest Wi-Fi |
| 999 | Management / Native (if used by policy) |

---

# 10. Real Banking Example

```text
              Core Switch
                    │
   ┌────────┬────────┬────────┬────────┐
   │        │        │        │
 HR(10) Finance(20) IT(30) ATM(40)
```

Suppose a malware infection occurs in HR.

Because HR is isolated:

- Finance remains protected.
- ATM traffic is unaffected.
- Firewall policies can further limit lateral movement.

---

# 11. VLAN Best Practices

- Do not use VLAN 1 for user devices.
- Use separate Management VLANs.
- Use separate Guest VLANs.
- Limit VLAN access with firewall policies.
- Document VLAN assignments.
- Use descriptive VLAN names.
- Disable unused switch ports.
- Follow a consistent VLAN numbering scheme.

---

# 12. Common VLAN Problems

### Problem 1 – Wrong VLAN Assigned

Result:

User cannot communicate with the intended subnet.

### Problem 2 – Wrong IP Subnet

Example:

```text
PC:
192.168.20.10

Connected to:
VLAN10

Expected:
192.168.10.0/24
```

The host will not communicate correctly with other devices in VLAN 10.

### Problem 3 – Guest User Can Access Internal Servers

Likely causes:

- Missing firewall policy.
- Incorrect VLAN assignment.
- Misconfigured inter-VLAN routing.

---

# 13. VLAN Communication

Devices in the **same VLAN** can communicate at Layer 2 when their addressing and switching configuration are correct.

```text
PC1
192.168.10.10
VLAN10

        ↓ Layer 2

PC2
192.168.10.20
VLAN10
```

Devices in different VLANs require Layer 3 routing.

```text
VLAN10
   ↓
Layer 3 Device
   ↓
VLAN20
```

---

# 14. Inter-VLAN Communication – Introduction

VLAN 10 and VLAN 20 are separate Layer 2 broadcast domains.

Inter-VLAN communication requires a Layer 3 device such as:

- Router
- Layer 3 Switch
- Firewall

Example:

```text
HR VLAN10
    ↓
FortiGate
    ↓
Finance VLAN20
```

The firewall can apply security policies between the VLANs.

Detailed VLAN tagging, trunking, and inter-VLAN implementation will be covered in the next chapter.

---

# 15. VLAN Security Basics

VLANs provide segmentation, but **VLANs alone are not a complete security boundary**.

Use additional controls such as:

- Firewall policies
- ACLs
- Port security
- 802.1X
- DHCP Snooping
- Dynamic ARP Inspection
- BPDU Guard
- Restricted trunk VLANs
- Disabled unused ports

Example:

```text
Guest VLAN
     ↓
Firewall Policy
     ↓
Internet

X
     ↓
Internal Server VLAN
```

---

# 16. Common VLAN Misconfigurations

### Misconfiguration 1

PC is assigned to the wrong VLAN.

### Misconfiguration 2

VLAN exists on one switch but is missing on another switch.

### Misconfiguration 3

A trunk does not carry the required VLAN.

### Misconfiguration 4

Incorrect native VLAN configuration.

### Misconfiguration 5

Host IP subnet does not match the VLAN subnet.

### Misconfiguration 6

Inter-VLAN firewall policy is missing or incorrect.

---

# 17. VLAN Troubleshooting

Use a Layer 2 → Layer 3 → Security approach.

```text
PC
 ↓
Switch Port
 ↓
VLAN Assignment
 ↓
Trunk / VLAN Propagation
 ↓
Gateway
 ↓
Inter-VLAN Routing
 ↓
Firewall / ACL
 ↓
Destination
```

Useful checks:

```text
show vlan brief
show interfaces trunk
show mac address-table
show spanning-tree
```

Then verify:

```text
IP Address
Subnet Mask
Default Gateway
```

Test:

```text
ping <gateway>
ping <destination>
traceroute <destination>
```

---

# 18. Common Interview Questions

## Q1. What is a VLAN?

A VLAN is a logical Layer 2 segmentation that creates separate broadcast domains on the same physical switching infrastructure.

## Q2. Why are VLANs used?

- Improve security
- Reduce broadcast traffic
- Simplify management
- Improve scalability
- Separate departments

## Q3. Can devices in different VLANs communicate directly?

No.

They require **Inter-VLAN Routing** through a Layer 3 switch, router, or firewall.

## Q4. Does a VLAN reduce collision domains?

No. A switch already creates one collision domain per port. VLANs primarily divide **broadcast domains**.

## Q5. Which industries use VLANs?

Almost all enterprise environments:

- Banking
- Healthcare
- Manufacturing
- Retail
- Government
- Universities
- Cloud Data Centers

---

# Practical Cisco Commands

### Show VLANs

```text
show vlan brief
```

### Show trunk information

```text
show interfaces trunk
```

### Show MAC addresses

```text
show mac address-table
```

### Show STP information

```text
show spanning-tree
```

---

# 🧠 Memory Trick

**Think of a VLAN as separate apartments in one building.**

- **Building** = Physical Switch
- **Apartment** = VLAN
- **People in Apartment A** can't directly enter Apartment B without going through the **security desk (router/firewall)**.

---

# 📝 Chapter Summary

By the end of this chapter, you should understand:

- ✅ What a VLAN is
- ✅ Why VLANs exist
- ✅ Broadcast vs Collision Domains
- ✅ VLAN IDs
- ✅ Types of VLANs
- ✅ Enterprise VLAN Design
- ✅ Static vs Dynamic VLANs
- ✅ VLAN Best Practices
- ✅ VLAN Security Basics
- ✅ Common VLAN Troubleshooting Scenarios
- ✅ Inter-VLAN Communication basics
- ✅ Interview-ready explanations

---

# 🎯 Assignment – Test Yourself

Try answering these without looking back at the chapter.

### Question 1

What does VLAN stand for?

### Question 2

Explain the difference between a physical LAN and a logical LAN.

### Question 3

Why does each VLAN create a separate broadcast domain?

### Question 4

What is the difference between a broadcast domain and a collision domain?

### Question 5

A company has HR, Finance, IT, Servers, and Guest Wi-Fi. Design a VLAN plan for these departments.

### Question 6

What is the purpose of a Data VLAN?

### Question 7

Why is a Voice VLAN used for IP phones?

### Question 8

What is a Management VLAN?

### Question 9

What is the purpose of a Guest VLAN?

### Question 10

What is a Private VLAN (PVLAN)?

### Question 11

What is the difference between static and dynamic VLAN assignment?

### Question 12

Why should VLAN 1 generally not be used for user or management traffic?

### Question 13

Can two devices in different VLANs communicate directly at Layer 2?

### Question 14

A Guest user can access an internal server. List at least three possible causes.

### Question 15

A user reports that the network works when connected to one switch port but not another. Build a troubleshooting sequence.

### Question 16 – Interview Challenge

Explain the complete troubleshooting path:

```text
PC
 ↓
Switch Port
 ↓
VLAN
 ↓
Trunk
 ↓
Gateway
 ↓
Inter-VLAN Routing
 ↓
Firewall
 ↓
Destination
```

---

# 🧪 Practical Assignment – Lab Practice

## Lab 1 – Create Basic VLANs

Create:

```text
VLAN 10 = HR
VLAN 20 = Finance
VLAN 30 = IT
VLAN 40 = Servers
VLAN 100 = Guest
```

Verify:

```text
show vlan brief
```

---

## Lab 2 – Assign Access Ports

Assign:

```text
Gi0/1 → VLAN10
Gi0/2 → VLAN20
Gi0/3 → VLAN30
Gi0/4 → VLAN40
Gi0/5 → VLAN100
```

Verify the VLAN membership.

---

## Lab 3 – Same-VLAN Connectivity

Connect two PCs to VLAN 10.

```text
PC1
192.168.10.10
     │
   VLAN10
     │
   Switch
     │
   VLAN10
     │
PC2
192.168.10.20
```

Test:

```text
ping 192.168.10.20
```

Explain why communication works at Layer 2.

---

## Lab 4 – Different-VLAN Connectivity

Create:

```text
VLAN10 → 192.168.10.0/24
VLAN20 → 192.168.20.0/24
```

Place one host in each VLAN.

Attempt communication.

Then add a Layer 3 device:

```text
VLAN10
   ↓
Layer 3 Device / Firewall
   ↓
VLAN20
```

Test again.

Explain why the first attempt fails and why the second can succeed.

---

## Lab 5 – VLAN Troubleshooting

Intentionally create these failures:

1. Wrong VLAN assignment.
2. Wrong IP subnet.
3. Missing VLAN.
4. Missing VLAN from a trunk.
5. Incorrect gateway.
6. Missing firewall policy.

Troubleshoot each using:

```text
show vlan brief
show interfaces trunk
show mac address-table
```

Then use:

```text
ping
traceroute
```

Document:

```text
Symptom
   ↓
Layer 2 Check
   ↓
VLAN Check
   ↓
Layer 3 Check
   ↓
Security Check
   ↓
Root Cause
   ↓
Fix
```

---

# 🧠 Practical Design Challenge

Design a VLAN structure for a medium-sized enterprise:

```text
HR
Finance
IT
Servers
Voice
CCTV
Printers
IoT
Guest Wi-Fi
Management
```

For each VLAN define:

- VLAN ID
- Purpose
- Example subnet
- Security requirement
- Whether Internet access is allowed
- Whether access to other VLANs is allowed

Then draw the topology:

```text
                    Internet
                        │
                    Firewall
                        │
                    Core Switch
              ┌─────────┼─────────┐
              │         │         │
          Access SW  Access SW  Access SW
              │         │         │
          HR / IT    Finance    Guest / IoT
```

---

# 🚀 Next Chapter

## **Chapter 10 – VLAN Tagging & Trunking (802.1Q, Access Ports, Trunk Ports & Inter-VLAN Routing)**

In the next chapter, we'll go beyond VLAN concepts and explain **how VLAN traffic actually moves between switches**, including:

- IEEE 802.1Q tagging
- Access vs Trunk ports
- VLAN tag structure
- Native VLAN
- Allowed VLANs
- Router-on-a-Stick
- Layer 3 Switch routing
- Firewall Inter-VLAN routing
- Real packet flow
- Enterprise troubleshooting
- Interview questions

This chapter is essential because it connects VLAN theory to real enterprise implementations.
