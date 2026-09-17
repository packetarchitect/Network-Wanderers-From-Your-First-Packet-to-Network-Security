# 📘 Module 3 – Routing

# Chapter 15 – RIP (Routing Information Protocol) — Basic to Advanced

---

## 📚 What This Chapter Covers

- What is RIP?
- Why RIP was introduced
- Distance Vector routing
- RIP hop-count metric
- Maximum hop count
- RIPv1
- RIPv2
- RIPng
- RIPv1 vs RIPv2 vs RIPng
- RIP route advertisements
- RIP timers
- Split Horizon
- Route Poisoning
- Poison Reverse
- Triggered Updates
- Hold-down timers
- RIPv2 authentication
- Cisco RIP configuration
- RIP verification and troubleshooting
- FortiGate RIP overview and configuration
- RIP advantages and limitations
- Real-world use cases
- Common Interview Questions
- Hands-on Labs
- Quick Revision
- Chapter Summary

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain what RIP is and why it was created.
- Understand RIP as a Distance Vector routing protocol.
- Explain hop count and RIP's 15-hop limitation.
- Differentiate between RIPv1, RIPv2, and RIPng.
- Explain RIP timers and periodic route advertisements.
- Understand Split Horizon, Route Poisoning, Poison Reverse, Triggered Updates, and Hold-down.
- Configure RIPv2 on Cisco routers.
- Understand the basic RIP configuration approach on FortiGate.
- Verify and troubleshoot RIP operation.
- Explain RIP's advantages, limitations, and enterprise relevance.
- Answer RIP interview questions confidently.

---

# 1. What is RIP?

**RIP (Routing Information Protocol)** is a **Distance Vector dynamic routing protocol** used to exchange routing information between routers.

RIP allows routers to automatically learn reachable networks from neighboring routers instead of manually configuring every route.

### Interview Definition

> **RIP is a Distance Vector routing protocol that uses hop count as its metric and supports a maximum usable path of 15 hops.**

Example:

```text
        R1
        |
        | 1 hop
        |
        R2
        |
        | 1 hop
        |
        R3
        |
        | 1 hop
        |
        R4
```

For a network behind R4, R1 may learn the destination with a metric based on the number of routers/hops required to reach it.

---

# 2. Why Was RIP Introduced?

Before dynamic routing protocols were commonly used, administrators often relied heavily on static routes.

Consider:

```text
R1 -------- R2 -------- R3 -------- R4
```

With static routing, the administrator would need to configure routes manually.

As networks grew, this became difficult to maintain.

RIP provided a simple way for routers to:

- Exchange route information.
- Learn remote networks automatically.
- Update routes when topology information changed.
- Reduce manual route configuration.

### Important Point

RIP was designed for **simplicity**, not for large modern enterprise networks.

---

# 3. RIP as a Distance Vector Protocol

RIP belongs to the **Distance Vector** family.

The basic idea is:

> **"Tell me how far the destination is and which neighbor can reach it."**

A RIP router does not build a complete topology map like a link-state protocol.

Instead, it learns routing information from neighboring routers.

Example:

```text
R1
 |
 | "Network X is 2 hops away"
 |
R2
 |
 | "Network X is 1 hop away"
 |
Network X
```

R1 can learn the route through R2.

### Characteristics of Distance Vector Routing

- Uses information learned from neighbors.
- Periodically exchanges routing information.
- Uses a routing metric.
- Requires loop-prevention mechanisms.
- Generally simpler than link-state routing.

---

# 4. RIP Hop Count Metric

RIP uses **hop count** as its routing metric.

A hop represents a router that traffic must pass through toward the destination.

Example:

```text
R1 ---- R2 ---- R3 ---- Network A
```

From R1:

```text
Network A
   ↓
2 router hops
```

RIP compares paths based primarily on hop count.

### Example

```text
             R2
            /
R1 --------
            \
             R3
```

If:

```text
R1 → R2 → Network A = 2 hops

R1 → R3 → Network A = 2 hops
```

Both paths can have the same RIP metric.

---

# 5. RIP Maximum Hop Count

RIP has a major limitation:

> **15 is the maximum valid hop count.**

A metric of:

```text
1–15 = reachable
16   = unreachable
```

Therefore:

```text
1 hop   → reachable
5 hops  → reachable
10 hops → reachable
15 hops → reachable
16 hops → unreachable
```

This is one of the most important RIP interview facts.

### Why 16?

RIP uses **16 as infinity** to represent an unreachable network.

### Interview Tip

If asked:

> "What is the maximum number of hops supported by RIP?"

Answer:

> **15 hops. A metric of 16 means unreachable.**

---

# 6. RIP Versions

RIP has three important versions/concepts:

```text
RIPv1
RIPv2
RIPng
```

### RIPv1

RIPv1 is the original version of RIP.

Characteristics:

- Classful routing.
- Does not carry subnet mask information.
- Does not support VLSM properly.
- Does not support CIDR.
- Uses broadcast updates.
- Does not provide the modern authentication capabilities of RIPv2.

Because it is classful, RIPv1 has significant limitations in modern networks.

---

# 7. RIPv2

RIPv2 was introduced to overcome important limitations of RIPv1.

Characteristics:

- Classless routing.
- Carries subnet mask information.
- Supports VLSM.
- Supports CIDR.
- Uses multicast for RIP updates.
- Supports authentication.
- Supports route tagging and other improvements.

RIPv2 uses:

```text
224.0.0.9
```

for RIP routing updates.

### Why RIPv2 is More Useful

Consider:

```text
192.168.10.0/24
192.168.20.0/26
192.168.20.64/26
```

Classless routing is necessary to carry the prefix information correctly.

RIPv2 supports this.

---

# 8. RIPng

**RIPng** means **RIP Next Generation**.

It extends RIP functionality to IPv6.

```text
IPv4
  ↓
RIPv1 / RIPv2

IPv6
  ↓
RIPng
```

RIPng provides RIP-style Distance Vector routing for IPv6 networks.

---

# 9. RIPv1 vs RIPv2 vs RIPng

| Feature | RIPv1 | RIPv2 | RIPng |
|---|---|---|---|
| Addressing | IPv4 | IPv4 | IPv6 |
| Routing Type | Classful | Classless | IPv6 Distance Vector |
| VLSM | ❌ | ✅ | ✅ |
| CIDR | ❌ | ✅ | IPv6 prefix-based |
| Authentication | Limited/none in original design | ✅ | Supported through IPv6 mechanisms/configuration |
| Update Method | Broadcast | Multicast | IPv6 multicast |
| Multicast Address | N/A | 224.0.0.9 | FF02::9 |
| Maximum Metric | 15 | 15 | 15 |

### Memory Trick

```text
RIPv1 = Old + Classful
RIPv2 = IPv4 + Classless
RIPng = IPv6
```

---

# 10. RIP Route Advertisement

RIP routers periodically exchange routing information.

A simplified process:

```text
        R1
        |
        | RIP Update
        ↓
        R2
        |
        | RIP Update
        ↓
        R3
```

The update contains routing information about reachable networks.

A router receives an advertisement and evaluates:

- Destination network.
- Prefix information where supported.
- Hop count.
- Next-hop/interface information as applicable.

If the learned route is preferred, the router can install it in the routing table.

---

# 11. RIP Update Timer

RIP periodically sends routing updates.

The standard RIP update interval is:

> **30 seconds**

Conceptually:

```text
0 sec
  ↓
RIP update

30 sec
  ↓
RIP update

60 sec
  ↓
RIP update

90 sec
  ↓
RIP update
```

This periodic-update behavior is a defining characteristic of traditional Distance Vector routing.

---

# 12. RIP Timers

RIP uses several timers to manage learned routes.

| Timer | Typical Value | Purpose |
|---|---:|---|
| Update | 30 seconds | Periodic routing update |
| Invalid | 180 seconds | Route considered invalid if updates are not received |
| Hold-down | 180 seconds | Helps stabilize route changes |
| Flush | 240 seconds | Removes the route from the routing table |

### Important

These values are commonly associated with classic RIP implementations.

Always distinguish the **protocol defaults** from any vendor-specific configuration or implementation behavior.

---

# 13. Split Horizon

**Split Horizon** is a routing-loop prevention mechanism.

The basic rule is:

> A router does not advertise a route back through the interface from which it learned that route.

Example:

```text
Network A
   |
   |
  R1
   |
   |
  R2
```

Suppose R2 learns Network A from R1.

R2 should not advertise:

```text
"Network A is reachable through me"
```

back toward R1 on the same path.

### Why?

It helps prevent routing loops.

---

# 14. Route Poisoning

**Route Poisoning** is another loop-prevention mechanism.

When a route becomes unreachable, the router advertises it with an infinite metric.

For RIP:

```text
16 = unreachable
```

Example:

```text
Network A
   |
  R1
   X
  R2
```

If R1 loses Network A:

```text
Network A = 16
```

R1 can advertise the route as unreachable.

This helps neighboring routers remove or invalidate the failed path.

---

# 15. Poison Reverse

Poison Reverse is closely related to Split Horizon.

Instead of simply refusing to advertise a learned route back toward the source, a router can advertise the route back with an infinite metric.

For RIP:

```text
Network A = 16
```

Conceptually:

```text
R1 learns Network A from R2

R1 → R2:
"Network A = 16"
```

This explicitly tells the neighbor that the route should not be considered reachable through that path.

### Split Horizon vs Poison Reverse

| Feature | Split Horizon | Poison Reverse |
|---|---|---|
| Basic action | Do not advertise route back | Advertise route back with infinite metric |
| RIP infinity | Not applicable | 16 |
| Purpose | Prevent loops | Explicitly poison learned route |

---

# 16. Triggered Updates

Normally, RIP sends periodic updates.

However, waiting for the next periodic update can delay information about an important topology change.

A **Triggered Update** allows a router to send an update immediately after a significant route change.

Example:

```text
Normal:
Wait → 30-second update

Triggered:
Failure detected
      ↓
Immediate update
```

This can help routers learn about failures faster.

---

# 17. Hold-down Timer

A hold-down mechanism helps prevent unstable or incorrect route information from being accepted immediately after a route failure.

Example:

```text
Route failure
     ↓
Hold-down state
     ↓
Ignore questionable updates
     ↓
Network stabilizes
```

The purpose is to reduce the chance of routing instability and loops during convergence.

### Simple Memory Trick

```text
Split Horizon
= Don't send it back

Route Poisoning
= Mark it unreachable

Triggered Update
= Tell everyone immediately

Hold-down
= Wait before trusting unstable information
```

---

# 18. RIP Authentication

RIPv2 supports authentication.

Authentication can help ensure that routing updates are accepted only from trusted peers.

Depending on the implementation, RIPv2 authentication can include:

- Plain-text authentication.
- MD5 authentication.

### Why Authentication Matters

Without appropriate protection, a malicious or incorrectly configured device could potentially inject routing information.

Conceptually:

```text
Router A
   |
   | Authenticated RIP update
   ↓
Router B
```

Router B verifies the configured authentication parameters before accepting the update.

---

# 19. RIP Convergence

Convergence is the process by which routers update their routing information after a topology change.

Example:

```text
R1 ---- R2 ---- R3
          |
        Network A
```

If the path changes:

```text
Failure detected
      ↓
Route information changes
      ↓
Updates propagate
      ↓
Routers recalculate/install routes
      ↓
Network reaches a stable state
```

RIP is generally considered a **slow-converging** protocol compared with modern link-state protocols such as OSPF.

Its periodic updates and loop-prevention mechanisms contribute to its behavior.

---

# 20. Cisco RIPv2 Configuration

A basic Cisco RIPv2 configuration:

```text
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 192.168.10.0
Router(config-router)# network 10.1.1.0
Router(config-router)# no auto-summary
```

### Explanation

```text
router rip
```

Enters RIP configuration mode.

```text
version 2
```

Enables RIPv2.

```text
network 192.168.10.0
```

Enables RIP on matching interfaces/networks according to Cisco RIP behavior.

```text
no auto-summary
```

Disables automatic classful summarization.

---

# 21. Cisco RIP Verification Commands

Useful commands include:

### View Routing Table

```text
show ip route
```

RIP-learned routes are commonly marked with:

```text
R
```

Example:

```text
R    192.168.20.0/24 [120/2] via 10.1.1.2
```

Here:

```text
R       = RIP
120     = Administrative Distance
2       = RIP metric
```

### View RIP Protocol Information

```text
show ip protocols
```

This can show:

- Routing protocol.
- Version.
- Networks participating in RIP.
- Timers.
- Passive interfaces.
- Routing sources.

### View RIP Routes

```text
show ip route rip
```

### Debug RIP

```text
debug ip rip
```

Use debugging carefully, especially on production devices.

---

# 22. FortiGate RIP Configuration

FortiGate also supports RIP.

A basic RIP configuration can be structured as:

```text
config router rip
    config network
        edit 1
            set prefix 192.168.10.0 255.255.255.0
        next
    end
end
```

The exact available options can vary with FortiOS version and platform.

### Concept

```text
FortiGate
   |
   | RIP
   |
Neighbor Router
```

The FortiGate can participate in dynamic routing and learn/advertise routes through RIP.

---

# 23. RIP Troubleshooting

When RIP routes are not appearing, follow a structured approach.

## Step 1 — Check Interface Status

Cisco:

```text
show ip interface brief
```

Confirm interfaces are:

```text
up/up
```

---

## Step 2 — Check RIP Configuration

```text
show running-config
show ip protocols
```

Verify:

- RIP is enabled.
- Correct RIP version is configured.
- Correct networks are included.
- Interfaces are not unintentionally passive.

---

## Step 3 — Check Routing Table

```text
show ip route
```

Look for:

```text
R
```

If no RIP route appears, investigate the advertisement path.

---

## Step 4 — Check Neighbor Connectivity

Test the next-hop/neighbor:

```text
ping <neighbor-ip>
```

If the neighbor is unreachable, fix the underlying network first.

---

## Step 5 — Check RIP Updates

Use:

```text
debug ip rip
```

Look for:

- Updates being sent.
- Updates being received.
- Expected networks.
- Incorrect network advertisements.

---

## Step 6 — Check Authentication

If authentication is configured:

```text
Router A
   |
Authentication mismatch
   X
Router B
```

The routers may fail to accept each other's routing updates.

---

## Step 7 — Check Summarization / Version

RIPv1 and RIPv2 behave differently.

Verify:

```text
version 2
no auto-summary
```

when the design requires classless routing.

---

# 24. RIP Failure Scenario

Consider:

```text
LAN-A
  |
 R1
  |
 R2
  |
 R3
  |
LAN-C
```

Suppose R2 loses the path toward LAN-C.

A simplified sequence is:

```text
Failure
   ↓
Route becomes invalid
   ↓
Route poisoning / triggered update
   ↓
Neighbors receive unreachable information
   ↓
Alternative route considered if available
   ↓
Routing tables stabilize
```

### Interview Thinking

If an interviewer asks:

> "What happens when a RIP route fails?"

Discuss:

1. Failure detection.
2. Route metric becoming unreachable.
3. Route poisoning.
4. Triggered updates.
5. Loop-prevention behavior.
6. Route removal/flush according to timers.
7. Alternative route selection if one exists.

---

# 25. RIP Advantages

RIP has several advantages:

- Simple to understand.
- Easy to configure.
- Easy to troubleshoot.
- Low complexity.
- Useful for learning Distance Vector concepts.
- Supported by many network platforms.

It is therefore excellent for:

```text
Learning
   ↓
Labs
   ↓
Understanding Distance Vector Routing
```

---

# 26. RIP Limitations

RIP also has significant limitations:

### 1. Maximum 15-hop network diameter

```text
16 = unreachable
```

### 2. Slow convergence

Compared with modern routing protocols such as OSPF, RIP can converge slowly.

### 3. Periodic updates

Periodic routing updates consume bandwidth.

### 4. Hop count is a simple metric

RIP does not select routes based on:

- Bandwidth
- Delay
- Link quality
- Application requirements

Its primary metric is hop count.

### 5. Limited scalability

RIP is not designed for large enterprise networks.

### 6. Less sophisticated than modern protocols

Protocols such as OSPF and BGP provide capabilities better suited to larger and more complex environments.

---

# 27. RIP in Real-World Network Design

A simplified enterprise design might look like:

```text
                 Core
                  |
        +---------+---------+
        |                   |
     Branch A             Branch B
        |                   |
       RIP                 RIP
```

RIP can work in small environments where:

- The network is small.
- The number of hops is limited.
- Requirements are simple.
- Advanced routing features are not necessary.

In larger environments, organizations commonly use more scalable routing protocols.

### Important Perspective

RIP remains valuable for learning because it introduces concepts that appear throughout routing:

```text
Route Advertisement
        ↓
Metrics
        ↓
Convergence
        ↓
Loop Prevention
        ↓
Dynamic Route Selection
```

---

# 28. RIP and the Routing Table

A RIP-learned route on Cisco is typically displayed with `R`.

Example:

```text
R    192.168.30.0/24 [120/3] via 10.1.1.2
```

Break it down:

```text
R
↓
RIP

120
↓
Administrative Distance

3
↓
RIP metric / hop count

10.1.1.2
↓
Next hop
```

This connects the RIP protocol to the routing-table concepts learned in earlier routing chapters.

---

# 29. RIP Packet and Update Flow

A simplified update flow:

```text
        Router A
           |
           | RIP Update
           ↓
        Router B
           |
           | RIP Update
           ↓
        Router C
```

Router B receives route information from Router A and may advertise the learned information onward according to RIP's rules and loop-prevention mechanisms.

When a route changes:

```text
Topology Change
      ↓
Metric changes
      ↓
Triggered Update
      ↓
Neighbor receives information
      ↓
Routing table updated
```

---

# 30. RIP Troubleshooting Flow

Use this flow during labs or interviews:

```text
RIP route missing
      |
      v
Interface UP?
   /       \
 NO         YES
 |           |
Fix       Check RIP config
             |
             v
       Correct version?
          /       \
        NO         YES
        |           |
      Fix       Check network statements
                        |
                        v
                 Neighbor reachable?
                    /        \
                  NO          YES
                  |            |
                Fix        Check updates
                               |
                               v
                        Check authentication
                               |
                               v
                        Check routing table
```

### Practical Rule

Do not start with advanced RIP debugging.

First verify:

```text
Interface
   ↓
IP addressing
   ↓
Connectivity
   ↓
RIP configuration
   ↓
Routing updates
   ↓
Routing table
```

---

# 31. Best Practices for RIP

When using RIP:

- Prefer RIPv2 where IPv4 RIP is required.
- Use `no auto-summary` when the topology requires classless routing.
- Use authentication where supported and appropriate.
- Avoid RIP in networks that exceed its practical limitations.
- Use passive interfaces where appropriate.
- Monitor routing-table changes.
- Document the routing design.
- Use debugging carefully on production devices.
- Understand the 15-hop limit.
- Use RIP primarily where its simplicity is appropriate.

---

# 32. Common Interview Questions

### Q1. What is RIP?

RIP is a Distance Vector dynamic routing protocol that uses hop count as its routing metric.

---

### Q2. What is the maximum hop count in RIP?

The maximum reachable metric is **15 hops**.

A metric of **16 means unreachable**.

---

### Q3. What metric does RIP use?

RIP uses **hop count**.

---

### Q4. What is the difference between RIPv1 and RIPv2?

| RIPv1 | RIPv2 |
|---|---|
| Classful | Classless |
| Does not carry subnet mask | Carries subnet mask |
| No VLSM support | Supports VLSM |
| No CIDR support | Supports CIDR |
| Broadcast | Multicast |
| No modern authentication capability | Supports authentication |

---

### Q5. What multicast address does RIPv2 use?

```text
224.0.0.9
```

---

### Q6. What is RIPng?

RIPng is the IPv6 version of RIP.

---

### Q7. What are the default RIP timers?

Common classic RIP timer values are:

```text
Update   = 30 seconds
Invalid  = 180 seconds
Hold-down = 180 seconds
Flush    = 240 seconds
```

---

### Q8. What is Split Horizon?

Split Horizon prevents a router from advertising a route back through the interface from which it learned that route.

---

### Q9. What is Route Poisoning?

Route Poisoning advertises a failed route with an infinite metric.

For RIP:

```text
16 = unreachable
```

---

### Q10. What is Poison Reverse?

Poison Reverse advertises a learned route back toward the neighbor with an infinite metric instead of simply suppressing the advertisement.

---

### Q11. What is a Triggered Update?

A Triggered Update sends routing information immediately after a significant topology change instead of waiting for the next periodic update.

---

### Q12. What is Hold-down?

Hold-down helps prevent unstable or potentially incorrect route information from being accepted immediately after a route failure.

---

### Q13. What is the Administrative Distance of RIP on Cisco?

The commonly used Cisco Administrative Distance for RIP is:

```text
120
```

---

### Q14. What does `R` mean in the Cisco routing table?

`R` indicates a route learned through RIP.

---

### Q15. Why is RIP not suitable for large enterprise networks?

Because of limitations such as:

- 15-hop maximum.
- Slow convergence compared with modern protocols.
- Simple hop-count metric.
- Periodic updates.
- Limited scalability.

---

### Q16. Which is preferred within RIP: a 2-hop path or a 4-hop path?

The 2-hop path has the lower RIP metric and is therefore preferred, assuming the routes are otherwise comparable.

---

### Q17. What command enables RIPv2 on Cisco?

```text
router rip
version 2
```

---

### Q18. What command disables automatic summarization in Cisco RIP?

```text
no auto-summary
```

---

## 🎤 Interview Answer – Explain RIP in 3–5 Minutes

> **"RIP, or Routing Information Protocol, is a Distance Vector dynamic routing protocol. Its main metric is hop count, meaning the router chooses the path with the lowest number of hops. RIP supports a maximum reachable metric of 15 hops, while 16 represents an unreachable network.**
>
> **There are three important versions or forms to know: RIPv1, RIPv2, and RIPng. RIPv1 is classful and does not support VLSM or CIDR. RIPv2 is classless, supports VLSM and CIDR, supports authentication, and uses multicast address 224.0.0.9 for updates. RIPng extends the protocol to IPv6.**
>
> **RIP traditionally sends updates every 30 seconds and uses timers such as the invalid, hold-down, and flush timers to manage route information. Because Distance Vector protocols can experience routing loops, RIP uses mechanisms such as Split Horizon, Route Poisoning, Poison Reverse, and Triggered Updates.**
>
> **On Cisco routers, a basic RIPv2 configuration uses `router rip`, `version 2`, network statements, and often `no auto-summary`. RIP routes appear as `R` in the routing table and have an Administrative Distance of 120 on Cisco.**
>
> **RIP is simple and excellent for learning dynamic routing, but its 15-hop limit, slower convergence, simple hop-count metric, and limited scalability make it less suitable for large modern enterprise networks. Learning RIP is still important because it provides the foundation for understanding dynamic routing, metrics, convergence, and loop prevention before moving to protocols such as OSPF."**

---

## 🧠 Memory Trick

### RIP = **Routes In Periodically**

Remember:

```text
RIP
│
├── Distance Vector
├── Hop Count
├── 15 hops maximum
├── 16 = unreachable
├── 30 sec updates
├── RIPv1 = Classful
├── RIPv2 = Classless
├── RIPng = IPv6
└── Loop Prevention
      ├── Split Horizon
      ├── Route Poisoning
      ├── Poison Reverse
      └── Triggered Updates
```

### The 15 / 16 Rule

```text
15 = Reachable
16 = Unreachable
```

### Version Trick

```text
v1 → Old / Classful
v2 → IPv4 / Classless
ng → Next Generation / IPv6
```

---

## ⭐ Gold Interview Tip

If the interviewer asks:

> **"Why do we still learn RIP if enterprises don't normally use it for large networks?"**

A strong answer is:

> **"RIP is important because it provides a simple foundation for understanding dynamic routing. It teaches core concepts such as route advertisements, metrics, convergence, routing loops, Split Horizon, Route Poisoning, and triggered updates. Once those concepts are clear, it becomes easier to understand more advanced protocols such as OSPF and BGP."**

Do **not** answer only:

> "RIP is old."

Show that you understand **why the protocol still matters educationally**.

---

## 📝 Quick Revision Notes

```text
RIP
↓
Distance Vector
↓
Metric = Hop Count
↓
Maximum = 15 hops
↓
16 = Unreachable
```

### Versions

```text
RIPv1
→ Classful
→ Broadcast
→ No VLSM/CIDR

RIPv2
→ Classless
→ VLSM/CIDR
→ Multicast 224.0.0.9
→ Authentication

RIPng
→ IPv6
```

### Timers

```text
Update     = 30 sec
Invalid    = 180 sec
Hold-down  = 180 sec
Flush      = 240 sec
```

### Loop Prevention

```text
Split Horizon
Route Poisoning
Poison Reverse
Triggered Updates
Hold-down
```

### Cisco

```text
router rip
version 2
network <network>
no auto-summary
```

### Verification

```text
show ip route
show ip route rip
show ip protocols
debug ip rip
```

---

# 🎯 Assignment – Test Yourself

## Theory

1. What is RIP?
2. Why is RIP classified as a Distance Vector protocol?
3. What metric does RIP use?
4. What is the maximum reachable hop count?
5. What does a metric of 16 mean?
6. Explain RIPv1.
7. Explain RIPv2.
8. What is RIPng?
9. Compare RIPv1 and RIPv2.
10. What multicast address does RIPv2 use?
11. Explain the RIP timers.
12. What is Split Horizon?
13. What is Route Poisoning?
14. What is Poison Reverse?
15. What is a Triggered Update?
16. What is the purpose of a Hold-down timer?
17. What is RIP's Administrative Distance on Cisco?
18. Why is RIP not suitable for large enterprise networks?
19. What does `R` mean in a Cisco routing table?
20. Explain the basic Cisco RIPv2 configuration.

## Scenario Questions

### Scenario 1

```text
R1 ---- R2 ---- R3 ---- R4
```

R1 needs to reach a network behind R4.

**Question:** How does RIP allow R1 to learn the remote network?

---

### Scenario 2

A RIP route shows:

```text
R 192.168.50.0/24 [120/5]
```

Explain:

- `R`
- `120`
- `5`

---

### Scenario 3

A RIP route suddenly becomes unreachable.

Explain the role of:

- Route Poisoning.
- Triggered Updates.
- Hold-down.
- Flush timer.

---

### Scenario 4

You configured:

```text
router rip
version 2
network 192.168.10.0
```

but no RIP route is appearing.

List the troubleshooting steps you would perform.

---

# 🧪 Practical Assignment – Lab Practice

## Lab 1 — Basic RIPv2

Build:

```text
PC1
 |
R1 -------- R2
             |
            PC2
```

Example addressing:

```text
LAN-A = 192.168.10.0/24
R1-R2 = 10.1.1.0/30
LAN-B = 192.168.20.0/24
```

Configure RIPv2 on both routers.

Verify:

```text
show ip route
show ip route rip
show ip protocols
```

Test:

```text
ping
```

---

## Lab 2 — Three-Router RIP

Build:

```text
LAN-A
  |
 R1
  |
 R2
  |
 R3
  |
LAN-C
```

Tasks:

1. Configure IP addressing.
2. Configure RIPv2.
3. Verify learned routes.
4. Test end-to-end connectivity.
5. Observe RIP routing-table entries.

---

## Lab 3 — RIPv1 vs RIPv2

Create a topology using multiple subnets.

Test RIPv1 and observe its classful limitations.

Then configure RIPv2:

```text
router rip
version 2
no auto-summary
```

Observe the difference.

---

## Lab 4 — Failure and Convergence

Build:

```text
        R2
       /  \
      /    \
    R1      R3
```

Create an alternative path.

Tasks:

1. Configure RIPv2.
2. Verify both paths.
3. Shut down one link.
4. Observe routing changes.
5. Check:

```text
show ip route
```

6. Use RIP debugging carefully to observe updates.

---

## Lab 5 — Wireshark RIP Analysis

Capture RIP traffic.

Look for:

- RIP packets.
- Source and destination addresses.
- Routing entries.
- Metric values.
- Periodic advertisements.
- Triggered updates when applicable.

The goal is to connect:

```text
Configuration
      ↓
RIP Packet
      ↓
Route Advertisement
      ↓
Routing Table
```

---

# 🧠 Practical Design Challenge

Design a small company network:

```text
             HQ
              |
             R1
            /  \
           /    \
        R2       R3
        |         |
      Branch A  Branch B
```

Requirements:

- Each branch has its own LAN.
- All routers must learn remote networks dynamically.
- Use RIPv2.
- Use classless addressing.
- Configure `no auto-summary`.
- Verify route learning.
- Simulate one link failure.
- Document how the route changes.

### Design Questions

1. What networks will you advertise?
2. Which interfaces participate in RIP?
3. What happens when the R1-R2 link fails?
4. Which loop-prevention mechanisms help?
5. What are the limitations of using RIP in this design?
6. At what point would you consider moving to OSPF?

---

# 📝 Chapter Summary

RIP is one of the simplest dynamic routing protocols and an excellent introduction to Distance Vector routing.

The most important concepts are:

```text
RIP
 ↓
Distance Vector
 ↓
Hop Count
 ↓
15-hop maximum
 ↓
16 = unreachable
 ↓
Periodic Updates
 ↓
Loop Prevention
```

RIPv1 is classful, while RIPv2 introduced classless routing and authentication capabilities. RIPng extends the concept to IPv6.

The protocol's most important loop-prevention concepts are:

```text
Split Horizon
Route Poisoning
Poison Reverse
Triggered Updates
Hold-down
```

RIP is easy to configure and understand, but its limited hop count, convergence behavior, simple metric, and scalability limitations make it less suitable for large modern networks.

---

# 🚀 Next Chapter

## Chapter 16 – OSPF (Open Shortest Path First)

Next, we move from **Distance Vector** to **Link State** routing.

We'll cover:

- What is OSPF?
- Why OSPF was created
- Link-state routing
- OSPF areas
- Area 0
- Router ID
- Neighbor relationships
- OSPF packet types
- DR and BDR
- LSA types
- LSDB
- SPF algorithm
- OSPF cost
- Administrative Distance
- OSPF network types
- Passive interfaces
- Authentication
- Cisco configuration
- FortiGate OSPF
- Troubleshooting
- OSPF interview questions
- Practical EVE-NG labs

```text
Chapter 14
Dynamic Routing
      ↓
Chapter 15
RIP
      ↓
Chapter 16
OSPF
      ↓
Chapter 17+
Advanced Routing
```

---

# 🌐 Keep Wandering

You have now learned the first major dynamic-routing protocol.

The journey continues:

```text
Static Routing
      ↓
Dynamic Routing
      ↓
RIP
      ↓
OSPF
      ↓
BGP
      ↓
Enterprise Routing
      ↓
Network Security
      ↓
Firewall Routing
      ↓
Cloud Security
```

**Don't just memorize the commands. Understand what the router is actually doing with the route.**

That's where networking starts becoming real.
