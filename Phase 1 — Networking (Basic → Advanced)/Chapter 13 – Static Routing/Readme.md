# 📘 Module 3 – Routing

# Chapter 13 – Static Routing (Basic to Advanced)

---

# 📚 What This Chapter Covers

- What is Static Routing?
- Why Static Routing is Required
- How Static Routing Works
- Static Route Components
- Next-Hop vs Exit Interface
- Types of Static Routes
- Standard Static Route
- Default Static Route
- Floating Static Route
- Recursive Route Lookup
- Fully Specified Static Route
- Black Hole (Null) Route
- Route Summarization using Static Routes
- Administrative Distance
- Static Routing Packet Flow
- Enterprise WAN Design
- Static Routing with VPN
- Cisco Configuration
- FortiGate Configuration
- Verification Commands
- Troubleshooting
- Best Practices
- Real-World Enterprise Scenarios
- Common Interview Questions
- Hands-on Labs
- Key Takeaways
- Quick Revision Notes

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain static routing and its purpose.
- Configure static routes on Cisco and FortiGate.
- Understand different types of static routes.
- Troubleshoot routing issues using routing tables.
- Design branch office and VPN networks using static routing.
- Answer advanced static routing interview questions confidently.

---

# 1. What is Static Routing?

A **static route** is a route that is **manually configured** by the network administrator.

Unlike dynamic routing protocols, routers **do not automatically learn or update** static routes.

### Interview Definition

> **Static routing is the manual configuration of routes in a router or firewall, specifying the destination network and the next hop or outgoing interface used to reach that network.**

---

# 2. Why Do We Need Static Routing?

Imagine two offices:

```text
Head Office
192.168.10.0/24
        │
      Router A
        │
────────WAN────────
        │
      Router B
        │
Branch Office
192.168.20.0/24
```

Router A only knows:

```text
192.168.10.0/24
```

Router B only knows:

```text
192.168.20.0/24
```

Without additional routes:

❌ The routers cannot reach each other's LAN.

**Solution:** Configure static routes.

### Example

On Router A:

```text
Destination:
192.168.20.0/24

Next Hop:
10.1.1.2
```

On Router B:

```text
Destination:
192.168.10.0/24

Next Hop:
10.1.1.1
```

Now both networks can communicate.

---

# 3. How Static Routing Works

Suppose PC1 sends a packet:

```text
PC1
192.168.10.10

↓

Router A

↓

Router B

↓

Server
192.168.20.100
```

Router A checks the destination:

```text
Destination:
192.168.20.100
```

Routing table:

| Destination Network | Next Hop |
|---|---|
| 192.168.20.0/24 | 10.1.1.2 |

Router A forwards the packet to Router B.

Router B already has a connected route to:

```text
192.168.20.0/24
```

The packet reaches the server.

---

# 4. Components of a Static Route

Every static route has three important parts:

```text
Destination Network
        ↓
Subnet Mask / Prefix
        ↓
Next Hop OR Exit Interface
```

Example:

```text
192.168.20.0/24
        ↓
Next Hop
        ↓
10.1.1.2
```

Cisco syntax:

```text
ip route <destination> <mask> <next-hop | exit-interface>
```

---

# 5. Standard Static Route

A standard static route manually specifies how to reach a destination network.

Example:

```text
ip route 192.168.20.0 255.255.255.0 10.1.1.2
```

Meaning:

> To reach `192.168.20.0/24`, forward traffic toward next hop `10.1.1.2`.

Static routes are particularly useful when the path is stable and the administrator wants explicit control over routing.

---

# 6. Next-Hop Static Route

A **next-hop static route** tells the router to forward packets toward another router.

Cisco:

```text
ip route 192.168.20.0 255.255.255.0 10.1.1.2
```

### Advantages

- Commonly preferred for Ethernet networks.
- The router identifies the next-hop IP and resolves the next-hop MAC address when required.
- Clearly identifies the neighboring router used to reach the destination.

### Conceptual Flow

```text
Destination Network
192.168.20.0/24

        ↓

Next Hop
10.1.1.2

        ↓

Outgoing Ethernet Interface
```

---

# 7. Exit-Interface Static Route

Instead of specifying the next-hop IP, you can specify the outgoing interface.

Example:

```text
ip route 192.168.20.0 255.255.255.0 GigabitEthernet0/0
```

This tells the router:

> Send traffic for `192.168.20.0/24` out of `GigabitEthernet0/0`.

### Common Use Cases

- Point-to-point links
- Serial interfaces
- PPP links

On multi-access networks such as Ethernet, a next-hop route can make the intended next-hop relationship clearer than relying only on an exit interface.

---

# 8. Fully Specified Static Route

A **fully specified static route** uses both:

- Next-hop IP address
- Exit interface

Example:

```text
ip route 192.168.20.0 255.255.255.0 GigabitEthernet0/0 10.1.1.2
```

### Why Use It?

It explicitly identifies both:

```text
Where to send?
        ↓
Next-hop IP

How to send?
        ↓
Exit interface
```

This can eliminate ambiguity and is useful in more complex forwarding environments.

---

# 9. Default Static Route

When no more specific route matches, the router can use:

```text
0.0.0.0/0
```

Cisco:

```text
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

Meaning:

> "If I don't know where to send the packet, send it to my ISP."

### Real-Life Example

```text
Office LAN
     │
     ↓
FortiGate
     │
     ↓
ISP
     │
     ↓
Internet
```

The firewall commonly has a default route toward the ISP.

---

# 10. Floating Static Route

A **floating static route** is used as a **backup route**.

Example:

```text
Primary Path:
MPLS
AD = 1

Backup Path:
Internet VPN
AD = 10
```

Under normal operation:

```text
Router
  │
  ├── MPLS        ← Preferred
  │
  └── VPN         ← Backup
```

If the primary route becomes unavailable and the backup route is eligible:

```text
MPLS fails
   ↓
Backup static route
   ↓
Internet IPsec VPN
```

A floating static route uses a **higher Administrative Distance** than the preferred route so that it is not selected while the primary route is available.

---

# 11. Administrative Distance

Administrative Distance (**AD**) measures how trustworthy a route source is when a router has routes to the same destination from different sources.

**Lower AD = More preferred.**

Common values:

| Route Type | AD |
|---|---:|
| Connected | 0 |
| Static | 1 |
| eBGP | 20 |
| EIGRP (Internal) | 90 |
| OSPF | 110 |
| RIP | 120 |

We'll study Administrative Distance in greater depth in later routing chapters.

### Floating Static Route Example

```text
ip route 192.168.20.0 255.255.255.0 10.2.2.2 10
```

Here:

- Destination = `192.168.20.0/24`
- Next hop = `10.2.2.2`
- AD = `10`

Because `10` is higher than the normal static-route AD of `1`, this route can serve as a backup.

---

# 12. Recursive Route Lookup

Suppose the route is:

```text
Destination:
192.168.20.0/24

Next Hop:
10.1.1.2
```

The router must determine:

> "How do I reach `10.1.1.2`?"

It performs another routing-table lookup to resolve the next hop.

This is called a **recursive lookup**.

### Conceptual Process

```text
Packet destination
192.168.20.100
       ↓
Find route to 192.168.20.0/24
       ↓
Next hop = 10.1.1.2
       ↓
Find route to 10.1.1.2
       ↓
Determine exit interface
       ↓
Forward packet
```

A fully specified static route supplies both the next-hop IP and exit interface.

---

# 13. Black Hole (Null) Route

Sometimes you intentionally want traffic to be discarded.

Cisco:

```text
ip route 192.168.100.0 255.255.255.0 Null0
```

Traffic matching that destination is intentionally discarded.

### Common Purposes

- Prevent routing loops.
- Support route summarization.
- Discard unwanted or unreachable traffic.
- Provide a deliberate sink for a destination range.

### Concept

```text
Traffic
   ↓
192.168.100.0/24
   ↓
Null0
   ↓
DISCARDED
```

---

# 14. Static Route Summarization

Suppose several networks need to be represented:

```text
192.168.8.0/24
192.168.9.0/24
192.168.10.0/24
192.168.11.0/24
192.168.12.0/24
```

A summary should be chosen only when the networks are actually covered by a valid common prefix. For the five networks above, a single `/21` starting at `192.168.8.0` covers:

```text
192.168.8.0 – 192.168.15.255
```

So:

```text
192.168.8.0/21
```

can represent that entire address range, provided the routing design can safely use the summary.

### Benefits

- Smaller routing tables.
- Simpler management.
- Better scalability.

### Important Caution

A summary route is broader than each individual subnet. Use it only when the broader destination range can safely follow the same forwarding path.

---

# 15. Static Routing Packet Flow

Consider:

```text
PC1
192.168.10.10
      │
      ↓
Router A
10.1.1.1/30
      │
      │
10.1.1.2/30
      ↓
Router B
      │
      ↓
Server
192.168.20.100
```

### Forward Direction

```text
PC1
 ↓
Router A
 ↓
Static route lookup
 ↓
Next hop 10.1.1.2
 ↓
Router B
 ↓
Connected route
 ↓
Server
```

### Return Direction

```text
Server
 ↓
Router B
 ↓
Route to 192.168.10.0/24
 ↓
Router A
 ↓
PC1
```

### Key Lesson

**Forward routing + return routing = successful two-way communication.**

---

# 16. Enterprise WAN Design

A retail company has:

```text
                Head Office
                     │
              Core / Firewall
                     │
        ───────── MPLS WAN ─────────
          │          │          │
       Branch 1   Branch 2   Branch 3
```

Each branch may have:

- One LAN
- One router
- One FortiGate
- One MPLS connection
- Internet connectivity

Static routes can be used when the topology is small, stable, and predictable.

### Typical Design

```text
Branch LAN
    │
FortiGate / Router
    │
    ├── Static route → Head Office
    │
    ├── Default route → Internet
    │
    └── Floating static → VPN backup
```

As the number of sites and topology changes increases, dynamic routing becomes more attractive.

---

# 17. Static Routing with VPN

Static routes are frequently used around site-to-site VPN designs.

Example:

```text
Branch LAN
192.168.20.0/24
      │
   FortiGate
      │
   IPsec VPN
      │
   FortiGate
      │
Head Office LAN
192.168.10.0/24
```

The devices need routes that direct traffic for the remote LAN through the appropriate VPN path.

Example concept:

```text
192.168.10.0/24
        ↓
IPsec VPN interface/tunnel
```

and on the other side:

```text
192.168.20.0/24
        ↓
IPsec VPN interface/tunnel
```

### Important

Routing alone does not guarantee VPN connectivity. Depending on the design, you also need:

- Correct IPsec configuration
- Matching security/firewall policies
- Matching selectors or traffic definitions
- Appropriate NAT behavior
- Return routing

---

# 18. Static Routing on Cisco

### Standard Static Route

```text
ip route 192.168.20.0 255.255.255.0 10.1.1.2
```

### Exit-Interface Route

```text
ip route 192.168.20.0 255.255.255.0 GigabitEthernet0/0
```

### Fully Specified Route

```text
ip route 192.168.20.0 255.255.255.0 GigabitEthernet0/0 10.1.1.2
```

### Default Route

```text
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

### Floating Static Route

```text
ip route 192.168.20.0 255.255.255.0 10.2.2.2 10
```

### Null Route

```text
ip route 192.168.100.0 255.255.255.0 Null0
```

---

# 19. Static Routing on FortiGate

### GUI

```text
Network
   ↓
Static Routes
   ↓
Create New
```

Configure:

- Destination
- Gateway
- Interface
- Distance
- Priority

### CLI

```text
config router static
    edit 1
        set dst 192.168.20.0/24
        set gateway 10.1.1.2
        set device port1
    next
end
```

### Conceptual Mapping

| Static Route Element | FortiGate Setting |
|---|---|
| Destination | `dst` |
| Next Hop | `gateway` |
| Exit Interface | `device` |
| Administrative Distance | `distance` |
| Route Preference / Priority | `priority` |

---

# 20. Verification Commands

## Cisco

```text
show ip route
show running-config | include ip route
ping <destination-ip>
traceroute <destination-ip>
show ip interface brief
```

## FortiGate

```text
get router info routing-table all
diagnose ip route list
execute ping <ip>
```

These commands help answer:

```text
Does the route exist?
        ↓
Is the next hop reachable?
        ↓
Does the packet reach the destination?
```

---

# 21. Troubleshooting Static Routing

## Problem

Cannot reach a remote network.

Check:

1. Does the static route exist?
2. Is the destination network/prefix correct?
3. Is the next hop reachable?
4. Is the exit interface up?
5. Does the remote router have a return route?
6. Is a firewall policy blocking traffic?
7. Is NAT affecting the traffic?
8. Is another route more preferred?
9. Is the routing table selecting the expected path?

### Example

PC:

```text
192.168.10.10
```

Can ping:

```text
192.168.10.1
```

Cannot ping:

```text
192.168.20.10
```

Possible causes:

- Missing static route.
- Missing return route.
- Firewall blocking traffic.
- Wrong subnet mask.
- Unreachable next hop.
- Incorrect interface.
- More-specific or otherwise preferred route sending traffic elsewhere.

### Troubleshooting Flow

```text
Start
  ↓
Check source IP
  ↓
Check destination IP
  ↓
Check local gateway
  ↓
Check routing table
  ↓
Is route present?
  ├── NO → Configure/correct route
  │
  └── YES
       ↓
Check next-hop reachability
       ↓
Check interface status
       ↓
Check remote return route
       ↓
Check firewall policy / NAT
       ↓
Test ping
       ↓
Test traceroute
```

---

# 22. Best Practices

- Document every static route and its purpose.
- Configure return routes.
- Verify next-hop reachability.
- Use floating routes for redundancy where appropriate.
- Avoid unnecessary recursive lookups where a fully specified route is useful.
- Review routing tables regularly.
- Use consistent route documentation across branches.
- Avoid static routing as the primary design when the network becomes large and frequently changing.
- Test failover paths before relying on them in production.
- Use route summarization carefully and only when the summarized range is valid for the intended forwarding path.

---

# 23. Real-World Enterprise Scenarios

## Scenario 1 – Retail Branches

```text
                Head Office
                     │
                     │ MPLS
          ┌──────────┼──────────┐
          ↓          ↓          ↓
       Branch 1   Branch 2   Branch 3
```

Each branch can use static routes toward the Head Office when the topology is small and stable.

## Scenario 2 – Banking Branch

```text
ATM Network
     │
     ↓
Branch Router
     │
     ↓
MPLS
     │
     ↓
Head Office
     │
     ↓
Core Banking Server
```

Primary path:

```text
MPLS
```

Backup:

```text
Internet IPsec VPN
```

Static routing can determine which path is preferred under normal conditions and which path is used when the primary path is unavailable.

## Scenario 3 – Internet Default Route

```text
Branch LAN
     │
     ↓
FortiGate
     │
     ↓
ISP
     │
     ↓
Internet
```

The FortiGate uses a default route:

```text
0.0.0.0/0
```

toward the ISP.

---

# 24. Common Interview Questions

### Q1. What is static routing?

A manually configured route that tells a router or firewall how to reach a specific destination network.

### Q2. What is the difference between static and dynamic routing?

| Static Routing | Dynamic Routing |
|---|---|
| Manual configuration | Routes learned automatically |
| No routing-protocol overhead | Uses routing protocols |
| Predictable and controlled | Adapts to topology changes |
| Best for small/stable networks | Better for large/changing networks |

### Q3. What is a default route?

A route to `0.0.0.0/0` used when no more specific route exists.

### Q4. What is a floating static route?

A backup static route configured with a higher Administrative Distance than the preferred route.

### Q5. Why is a return route important?

Routing is bidirectional. Even if the forward path exists, the destination must also know how to send the reply back.

### Q6. Difference between next-hop and exit-interface static route?

A next-hop route specifies the neighboring router's IP. An exit-interface route specifies the interface through which the packet leaves.

### Q7. What is a fully specified static route?

A route that specifies both the outgoing interface and next-hop IP address.

### Q8. What is recursive lookup?

An additional routing-table lookup used to determine how to reach the next-hop IP specified by a route.

### Q9. What is Administrative Distance?

A value used to compare the trustworthiness of route sources. Lower values are preferred.

### Q10. How does a floating static route provide redundancy?

The backup static route uses a higher AD than the primary route, so it can become preferred when the primary route is removed.

### Q11. What is a Null0 route?

A route that intentionally forwards matching traffic to a null interface so the traffic is discarded.

### Q12. When should you use static routing?

When the topology is small, stable, predictable, or when an administrator needs a manually controlled path or backup route.

---

# 🎤 Interview Answer – Explain Static Routing in 3–5 Minutes

> **"Static routing is the manual configuration of routes on a router or firewall. A static route specifies a destination network and either a next-hop IP address or an outgoing interface. Static routes are useful in small and stable networks, branch offices, VPN designs, and situations where we need explicit control over traffic paths.**
>
> **There are several important types. A standard static route points to a particular destination network. A default route, 0.0.0.0/0, is used when no more specific route exists. A floating static route provides a backup path by using a higher Administrative Distance than the primary route. A fully specified static route contains both the next-hop IP and exit interface. We can also use Null0 routes to intentionally discard traffic and support designs such as route summarization.**
>
> **When troubleshooting static routing, I first check the routing table, destination prefix, next-hop reachability, and interface status. Then I verify the remote side has a return route and check firewall policies and NAT if a security device is involved. The most important point is that routing must work in both directions. Static routing is simple and predictable, but as a network becomes larger and more dynamic, dynamic routing protocols become more suitable."**

---

# 🧠 Memory Trick

Remember:

```text
STATIC = S N A P
```

**S** → Specific destination  
**N** → Next hop  
**A** → Administrative Distance  
**P** → Path control

Route types:

```text
STANDARD → Specific network
DEFAULT   → 0.0.0.0/0
FLOATING  → Backup
FULL      → Interface + Next Hop
NULL      → Discard
```

---

# ⭐ Gold Interview Tip

When an interviewer asks:

> **"PC can ping its gateway but cannot reach a remote network. What do you check?"**

Do not immediately say **"check the static route."**

Give a structured troubleshooting sequence:

```text
Source IP
   ↓
Default Gateway
   ↓
Routing Table
   ↓
Destination Route
   ↓
Next-Hop Reachability
   ↓
Interface Status
   ↓
Return Route
   ↓
Firewall Policy
   ↓
NAT
   ↓
Ping / Traceroute
```

This demonstrates **real troubleshooting methodology**, not just memorized commands.

---

# 📝 Quick Revision Notes

| Concept | Remember |
|---|---|
| Static Route | Manually configured |
| Destination | Network to reach |
| Next Hop | Neighboring router |
| Exit Interface | Outgoing interface |
| Fully Specified | Interface + Next Hop |
| Default Route | `0.0.0.0/0` |
| Floating Route | Backup route |
| Static AD | `1` |
| Connected AD | `0` |
| Recursive Lookup | Resolve next hop |
| Null Route | Discard traffic |
| Return Route | Required for two-way communication |
| Route Summarization | Represent multiple networks with a broader prefix |

---

# 🎯 Assignment – Test Yourself

## Theory

1. Explain how static routing works.
2. Differentiate between next-hop, exit-interface, and fully specified static routes.
3. Explain recursive route lookup.
4. Why are return routes necessary?
5. When would you use a floating static route?
6. What is a default route?
7. What is Administrative Distance?
8. Why is a lower AD preferred?
9. What is a Null0 route?
10. What should you check when a remote network is unreachable?

## Configuration Practice

Write Cisco commands for:

1. A route to `192.168.20.0/24` via `10.1.1.2`.
2. A default route via `203.0.113.1`.
3. A floating static route via `10.2.2.2` with AD `10`.
4. A Null0 route for `192.168.100.0/24`.
5. A fully specified route to `192.168.30.0/24` through `GigabitEthernet0/0` and next hop `10.1.1.2`.

---

# 🧪 Practical Assignment – Lab Practice

## Lab Topology

Build:

```text
PC1
192.168.10.10
      │
   Router A
10.1.1.1/30
      │
10.1.1.2/30
      │
   Router B
      │
Server
192.168.20.100
```

## Tasks

### Task 1 – Configure IP Addresses

```text
PC1:
192.168.10.10/24

Router A LAN:
192.168.10.1/24

Router A WAN:
10.1.1.1/30

Router B WAN:
10.1.1.2/30

Router B LAN:
192.168.20.1/24

Server:
192.168.20.100/24
```

### Task 2 – Configure Static Routes

Router A:

```text
ip route 192.168.20.0 255.255.255.0 10.1.1.2
```

Router B:

```text
ip route 192.168.10.0 255.255.255.0 10.1.1.1
```

### Task 3 – Verify

```text
show ip route
ping 192.168.20.100
traceroute 192.168.20.100
```

### Task 4 – Floating Static Route

Add a second WAN path.

1. Verify the primary route is active.
2. Shut down the primary WAN interface.
3. Check the routing table.
4. Verify the backup path is selected.
5. Restore the primary path.
6. Verify normal routing returns.

### Task 5 – Wireshark

Capture traffic and identify:

- Source IP
- Destination IP
- TTL
- ICMP Echo Request
- ICMP Echo Reply
- Hop-by-hop forwarding

### Task 6 – Troubleshooting Challenge

Intentionally introduce:

- Wrong next hop
- Missing return route
- Shutdown interface
- Incorrect subnet mask

Then troubleshoot each failure systematically.

---

# 🧠 Practical Design Challenge

Design:

```text
Head Office
     │
     ├── MPLS ───────── Branch
     │
     └── Internet ───── IPsec VPN ───── Branch
```

Requirements:

- MPLS must be the primary path.
- IPsec VPN must be the backup.
- Branch Internet traffic should use the local ISP.
- Head Office traffic should use the WAN/VPN path.
- The design must support failover.
- Document all routes and their purpose.

**Challenge:** Explain which routes you would configure and how Administrative Distance would influence path selection.

---

# 🚀 Next Chapter

## Chapter 14 – Dynamic Routing (Basic to Advanced)

You'll learn:

- Why dynamic routing is needed
- Static vs Dynamic Routing
- Distance Vector
- Link State
- Path Vector
- Route convergence
- Metrics
- Routing protocol comparison
- Enterprise routing design
- Troubleshooting
- Interview questions

This chapter prepares you for **RIP, OSPF, and BGP**, which are among the most frequently asked routing topics in enterprise networking interviews.

---

# 🌐 Keep Wandering

Static routing is the foundation for understanding why dynamic routing protocols exist.

Once you understand:

```text
Static Routes
     ↓
Routing Tables
     ↓
Route Selection
     ↓
Administrative Distance
     ↓
Dynamic Routing
     ↓
RIP → OSPF → BGP
```

you can start thinking like a network engineer rather than simply memorizing routing commands.
