# 📘 Module 3 – Routing

# Chapter 14 – Dynamic Routing (Basic to Advanced)

---

# 📚 What This Chapter Covers

- What is Dynamic Routing?
- Why Dynamic Routing is Required
- Static Routing vs Dynamic Routing
- How Dynamic Routing Works
- Routing Protocols Overview
- Route Learning Process
- Route Advertisement
- Route Convergence
- Routing Metrics
- Administrative Distance
- Distance Vector Routing
- Link State Routing
- Path Vector Routing
- Hybrid Routing (Introduction)
- Routing Tables
- Neighbor Relationships
- Routing Loops
- Split Horizon
- Route Poisoning
- Triggered Updates
- Equal Cost Multi-Path (ECMP)
- Load Balancing
- Scalability
- Enterprise Routing Design
- Dynamic Routing in Data Centers
- Dynamic Routing with Firewalls
- Cisco Configuration Basics
- FortiGate Dynamic Routing Overview
- Troubleshooting
- Best Practices
- Common Interview Questions
- Hands-on Labs
- Key Takeaways
- Quick Revision Notes

---

# 🎯 Learning Objectives

After completing this chapter, you will be able to:

- Explain why dynamic routing is used.
- Differentiate between static and dynamic routing.
- Understand how routers exchange routing information.
- Compare Distance Vector, Link State, and Path Vector protocols.
- Explain routing convergence.
- Understand routing metrics and administrative distance.
- Troubleshoot common dynamic routing issues.
- Answer dynamic routing interview questions confidently.

---

# 1. What is Dynamic Routing?
Dynamic routing is the process where routers automatically learn, update, and maintain routing information by exchanging routes with other routers using routing protocols.
Unlike static routing, administrators do not manually configure every destination network.
Interview Definition
Dynamic routing is a method where routers automatically discover and update routes using routing protocols such as RIP, OSPF, or BGP, allowing networks to adapt to topology changes without manual intervention.


# 2. Why Do We Need Dynamic Routing?
Imagine a company with 100 branch offices.
        HQ
      /  |  \
     /   |   \
Branch Branch Branch
  1      2      3
      ...
    Branch100
Every branch has:
- Local LAN
- Router
- WAN connection
If static routing is used:
- Every router must know every network.
- Hundreds or thousands of routes must be configured manually.
- Any network change requires manual updates.
This becomes:
❌ Time-consuming
❌ Error-prone
❌ Difficult to scale
Dynamic Routing Solution
Routers automatically exchange routing information.
Router A
    │
"I know 192.168.10.0/24"

↓

Router B

↓

"I know 192.168.20.0/24"

↓

Router C
Each router builds its routing table automatically.
Real Enterprise Example
A retail chain has:
- 500 stores
- 2 Data Centers
- MPLS WAN
- Backup Internet VPN
Whenever a new branch is added:
With Static Routing:
Every router needs manual updates.
With Dynamic Routing:
The new branch advertises its routes automatically.

# 3. Static Routing vs Dynamic Routing
Feature	Static Routing	Dynamic Routing
Configuration	Manual	Automatic
Scalability	Low	High
Network Changes	Manual Updates	Automatic Updates
CPU Usage	Low	Higher
Bandwidth Usage	None	Routing Updates
Best For	Small Networks	Medium & Large Networks



# 4. How Dynamic Routing Works
Suppose:
PC
 │
Router A
 │
Router B
 │
Router C
 │
Server
Initially:
Router A only knows:
192.168.10.0/24
Router B knows:
10.10.10.0/30
Router C knows:
172.16.1.0/24
Dynamic routing begins.
Routers exchange routing information.
Eventually:
Every router learns every reachable network.

# 5. Route Advertisement
Routing protocols advertise:
- Destination Network
- Prefix Length
- Metric
- Next Hop (protocol dependent)
Example:
Router A

↓

Advertising

192.168.10.0/24
Neighbor routers add this route to their routing tables if it is the best available path.

# 6. Neighbor Relationship
Before exchanging routes, many routing protocols first establish neighbor relationships.
Example:
Router A

⇄ Hello Packets ⇄

Router B
Neighbors verify:
- Reachability
- Compatibility
- Timers
- Authentication (if configured)
Protocols like OSPF require neighbor formation, while RIP does not maintain formal adjacencies in the same way.

# 7. Route Convergence
Convergence is the process by which all routers agree on the current network topology after a change.
Example:
Router A

↓

Router B

↓

Router C
Suppose:
Router B loses its connection.
Routers detect the failure.
↓
Remove invalid routes.
↓
Calculate new paths.
↓
Install updated routes.
Once every router has the same view of the network:
✅ The network has converged.

# 8. Routing Metrics
Sometimes multiple paths exist.
Example:
          Router B
        /
Router A
        \
          Router C
Which path should be used?
Routing protocols calculate metrics.
Common metrics include:
- Hop Count
- Bandwidth
- Delay
- Cost
- Reliability
- Load
Different routing protocols use different metrics.

# 9. Administrative Distance (Review)
Sometimes the same destination is learned from multiple routing sources.
Example:
192.168.20.0/24

↓

Static Route

↓

OSPF

↓

RIP
The router first compares Administrative Distance (AD).
Lower AD wins.
Common values:
Route Source	AD
Connected	0
Static	1
eBGP	20
EIGRP (Internal)	90
OSPF	110
RIP	120


If AD is equal, the protocol's metric is then used.

# 10. Types of Dynamic Routing Protocols
There are three major categories.
1. Distance Vector
Idea:
"Tell me how far the destination is."

Characteristics:
- Periodic updates.
- Simpler.
- Lower resource requirements.
Example:
- RIP
Metric:
Hop Count
2. Link State
Idea:
"I'll build a complete map of the network."

Characteristics:
- Topology database.
- Fast convergence.
- Scales well.
Example:
- OSPF
- IS-IS
Metric:
Cost
3. Path Vector
Idea:
"I'll advertise the complete path."

Characteristics:
- Internet-scale routing.
- Policy-based decisions.
Example:
- BGP
Metric:
AS Path and other path attributes.
Comparison
Feature	Distance Vector	Link State	Path Vector
Example	RIP	OSPF	BGP
Network View	Neighbor Information	Full Topology	AS Path Information
Convergence	Slow	Fast	Moderate
Scalability	Small Networks	Enterprise	Internet



# 11. Routing Loops
Sometimes routers incorrectly forward packets in circles.
Example:
Router A

↓

Router B

↓

Router C

↓

Router A
Packet:
A → B → C → A
Until TTL reaches zero.
Routing protocols include loop prevention mechanisms.

# 12. Loop Prevention Mechanisms
Common techniques:
Split Horizon
A router does not advertise a route back out the interface from which it learned it.
Route Poisoning
A failed route is advertised with an infinite metric so other routers know it is unreachable.
Triggered Updates
Instead of waiting for the next scheduled update, routers immediately advertise important topology changes.

# 13. Equal Cost Multi-Path (ECMP)
Suppose Router A has two identical routes.
      Router B

     /

Router A

     \

      Router C
Both paths:
- Same metric
- Same Administrative Distance
The router can install both routes and perform load balancing.

# 14. Enterprise Example
A multinational company has:
Branches

↓

Regional Offices

↓

Head Office

↓

Data Center
OSPF is used inside the enterprise.
BGP connects to ISPs.
Static routes are used for small remote sites.
This combination provides scalability and resilience.

# 15. Dynamic Routing on Firewalls
Modern firewalls also support dynamic routing.
Examples:
FortiGate
Supports:
- RIP
- OSPF
- BGP
- IS-IS (on supported platforms/versions)
Used for:
- SD-WAN
- Data Centers
- IPsec VPNs
- High Availability
Palo Alto
Supports:
- OSPF
- BGP
- RIP
- Static Routing
This allows firewalls to participate directly in enterprise routing.

# 16. Troubleshooting
Problem
Routers are not learning routes.
Check:
- Neighbor relationship.
- Routing protocol enabled.
- Network statements.
- Authentication.
- Interface status.
- Passive interfaces.
- ACL or firewall rules.
Cisco Commands
show ip route
show ip protocols
show ip ospf neighbor
show ip bgp
FortiGate Commands
get router info routing-table all
get router info ospf neighbor
get router info bgp summary
Best Practices
- Use dynamic routing for medium and large networks.
- Summarize routes whenever possible.
- Use authentication between routing neighbors.
- Monitor convergence times.
- Keep routing designs simple and well documented.
- Combine static and dynamic routing where appropriate.
  
Common Interview Questions
Q1. What is dynamic routing?
Dynamic routing is the automatic exchange and maintenance of routing information between routers using routing protocols.
Q2. Why is dynamic routing preferred in large networks?
Because it automatically adapts to topology changes and scales much better than manually configured static routes.
Q3. What are the three main types of routing protocols?
- Distance Vector
- Link State
- Path Vector
Q4. What is convergence?
The process by which all routers update their routing information and agree on a consistent view of the network after a topology change.
Q5. What is the difference between Administrative Distance and Metric?
Administrative Distance	Metric
Compares different routing sources	Compares routes within the same routing protocol
Lower is preferred	Lower is usually preferred (protocol dependent)


# 🎤 Interview Answer – Explain Dynamic Routing in 3–5 Minutes
"Dynamic routing allows routers to automatically exchange and maintain routing information using protocols such as RIP, OSPF, and BGP. Unlike static routing, it adapts automatically to network changes, making it ideal for medium and large enterprise networks. Routers advertise routes, build routing tables, and use metrics to select the best path. When multiple routing sources provide the same destination, Administrative Distance determines the preferred source. Dynamic routing also includes loop prevention mechanisms such as Split Horizon, Route Poisoning, and Triggered Updates to improve network stability and reliability."

# 📝 Quick Revision Notes
✅ Dynamic Routing = Automatic route learning
✅ Best for medium and large networks
✅ Three protocol families:
- Distance Vector
- Link State
- Path Vector
✅ Convergence = Network reaches a consistent routing state
✅ Administrative Distance = Chooses between routing sources
✅ Metric = Chooses the best path within a protocol
✅ ECMP = Load balancing across equal-cost paths
✅ Loop prevention = Split Horizon, Route Poisoning, Triggered Updates

# 🎯 Assignment – Test Yourself
Theory
1. Explain the difference between static and dynamic routing.
2. Compare Distance Vector, Link State, and Path Vector protocols.
3. What is convergence, and why is it important?
4. Explain the difference between Administrative Distance and Metric.
5. Describe three routing loop prevention mechanisms.
Practical Lab
Build this topology:
       Router A
      /        \
 Router B     Router C
      \        /
       Router D
Tasks:
1. Configure a dynamic routing protocol (RIP or OSPF).
2. Verify neighbors (if applicable).
3. Check the routing table.
4. Disconnect one link and observe convergence.
5. Measure how quickly routes are updated.
6. Capture routing protocol packets in Wireshark to analyze route advertisements.

🚀 Next Chapter
Chapter 15 – RIP (Routing Information Protocol) (Basic to Advanced)
We'll cover:
- RIP Fundamentals
- RIPv1 vs RIPv2
- Hop Count Metric
- Timers
- Split Horizon
- Route Poisoning
- Triggered Updates
- Authentication
- Configuration
- Troubleshooting
- Enterprise limitations
- Interview questions
- Practical labs
This chapter introduces the first dynamic routing protocol and lays the groundwork for more advanced protocols like OSPF and BGP.


---

# 🧠 Memory Trick

Remember the three routing families:

```text
DISTANCE VECTOR
"How far?"

LINK STATE
"What does the network look like?"

PATH VECTOR
"What path and policy?"
```

### AD vs Metric

```text
Administrative Distance
        ↓
Which routing source?

Metric
        ↓
Which path within that protocol?
```

### Convergence

```text
CHANGE
 ↓
DETECT
 ↓
ADVERTISE
 ↓
CALCULATE
 ↓
INSTALL
 ↓
CONVERGE
```

### Loop Prevention

```text
SRT

S → Split Horizon
R → Route Poisoning
T → Triggered Updates
```

---

# ⭐ Gold Interview Tip

If the interviewer asks:

> **"What happens when a link fails in a dynamic-routing network?"**

Answer as a sequence:

```text
Link Failure
     ↓
Failure Detection
     ↓
Routing Information Updated
     ↓
Update Propagated
     ↓
New Path Calculated
     ↓
Best Route Selected
     ↓
Routing Table Updated
     ↓
Packet Forwarding Uses New Path
     ↓
Network Converges
```

Then mention that the exact process and timing depend on the routing protocol and configuration.

---

# 📝 Chapter Summary

Dynamic routing allows routers to exchange routing information automatically and adapt to topology changes.

The three major protocol categories are:

```text
Distance Vector → RIP
Link State      → OSPF / IS-IS
Path Vector     → BGP
```

The key distinction is:

```text
Administrative Distance
→ compares routing sources

Metric
→ compares paths within a routing protocol
```

---

# 🧠 Practical Design Challenge

Design an enterprise network with:

```text
                 ISP 1
                   │
                Router
                   │
        ┌──────────┴──────────┐
        │                     │
   Data Center           Head Office
        │                     │
        └──────────┬──────────┘
                   │
                Branches
```

Requirements:

- Use dynamic routing inside the enterprise.
- Use BGP for ISP connectivity.
- Use static routing where operationally appropriate.
- Provide redundant paths.
- Support ECMP where the design permits.
- Define routing boundaries.
- Document the purpose of each routing method.

**Challenge:** Explain why you would choose each routing method and how the design would respond to a link failure.

---

# 🚀 Next Chapter

## Chapter 15 – RIP (Routing Information Protocol) (Basic to Advanced)

You'll learn:

- RIP Fundamentals
- RIPv1 vs RIPv2
- Hop Count Metric
- Timers
- Split Horizon
- Route Poisoning
- Triggered Updates
- Authentication
- Configuration
- Troubleshooting
- Enterprise limitations
- Interview questions
- Practical labs

This chapter introduces the first dynamic routing protocol and lays the groundwork for more advanced protocols like **OSPF and BGP**.

---

# 🌐 Keep Wandering

Dynamic routing is where networking starts becoming truly **adaptive**.

Instead of thinking:

```text
"I configured a route."
```

start thinking:

```text
"How does the network learn,
select, replace, and validate routes?"
```

That mindset will make the next chapters—**RIP → OSPF → BGP**—much easier to understand.
