# Chapter 3 – TCP/IP Model (Basic to Advanced)

---

# What is the TCP/IP Model?

**TCP/IP** stands for:

> **Transmission Control Protocol / Internet Protocol**

The TCP/IP model is the **practical networking model used on the Internet**, unlike the OSI model, which is mainly a reference model.

Every device connected to the Internet uses the TCP/IP protocol suite.

---

# Why Do We Need TCP/IP?

Imagine you send a WhatsApp message.

How does it travel?

```text
Your Phone
     │
Wi-Fi Router
     │
ISP
     │
Internet
     │
Google/Meta Server
     │
Friend's Phone
```

TCP/IP defines **how data is packaged, addressed, transmitted, routed, and received**.

---

# OSI vs TCP/IP

| OSI Model | TCP/IP Model |
|---|---|
| 7 Layers | 4 Layers |
| Reference Model | Practical Model |
| Developed by ISO | Developed by DARPA |
| Mainly for understanding | Used everywhere |

---

# TCP/IP Layers

| TCP/IP Layer | Equivalent OSI Layers |
|---|---|
| Application | OSI 7 + 6 + 5 |
| Transport | OSI 4 |
| Internet | OSI 3 |
| Network Access | OSI 2 + 1 |

> **Note:** Some resources use alternative names such as **Process/Application**, **Host-to-Host/Transport**, and **Host-to-Network/Network Access** for the TCP/IP layers.

---

# 1. Application Layer

The TCP/IP Application Layer combines three OSI layers:

- Application
- Presentation
- Session

## Responsibilities

- User communication
- File transfer
- Email
- Web browsing
- DNS resolution
- Remote login
- User-interface related services
- Application-level network communication

## Common Protocols

| Protocol | Port | Purpose |
|---|---:|---|
| HTTP | 80 | Web browsing |
| HTTPS | 443 | Secure web browsing |
| FTP | 21 | File Transfer |
| SFTP | 22 | Secure File Transfer |
| SSH | 22 | Remote login |
| DNS | 53 | Name resolution |
| DHCP | 67/68 | IP assignment |
| SMTP | 25 | Sending email |
| IMAP | 143 | Receiving email |
| POP3 | 110 | Receiving email |
| SMB | 445 | File sharing |
| SNMP | 161/162 | Network monitoring |
| NTP | 123 | Time synchronization |

## Examples

### HTTP and HTTPS

**HTTP** stands for Hypertext Transfer Protocol and is used for communication between web browsers and web servers.

**HTTPS** combines HTTP with TLS to provide secure communication.

HTTPS is especially important when:

- Signing in
- Authenticating
- Submitting forms
- Performing banking transactions

### SSH

**SSH (Secure Shell)** provides secure remote terminal access.

It is preferred over Telnet because it can maintain an encrypted connection.

### NTP

**NTP (Network Time Protocol)** synchronizes computer clocks with a standard time source.

Accurate time is especially important in environments such as banking, logging, authentication, and security monitoring.

---

# 2. Transport Layer

The TCP/IP Transport Layer corresponds to the OSI Transport Layer.

It is one of the **most important interview topics**.

The two main protocols are:

- TCP
- UDP

The Transport Layer is responsible for **end-to-end communication** and provides transport services to upper-layer applications.

---

# TCP (Transmission Control Protocol)

TCP provides:

> **Reliable communication**

TCP is a **connection-oriented** transport protocol.

## TCP Features

- Connection-oriented
- Reliable
- Sequencing
- Acknowledgments
- Flow control
- Error recovery
- Retransmission
- Ordered delivery
- Full-duplex communication
- Stream-oriented communication

### Real Example

Suppose you are performing a banking transaction:

```text
Transfer ₹10,000
```

Can you afford important application data to be lost or delivered incorrectly?

No.

TCP provides mechanisms such as acknowledgments, sequencing, retransmission, and flow control to provide reliable transport.

> **Important:** TCP provides reliable transport. It does not itself provide application-level security or encryption.

---

# UDP (User Datagram Protocol)

UDP provides a lightweight, connectionless transport service.

It does not provide TCP-style:

- Connection establishment
- Acknowledgments
- Retransmission
- Ordering guarantees

This makes UDP useful where low overhead and speed are more important than guaranteed delivery.

## Real Examples

- Video calls
- Gaming
- IPTV
- Voice calls
- DNS queries

---

# TCP vs UDP

| TCP | UDP |
|---|---|
| Reliable | No delivery guarantee |
| Connection-oriented | Connectionless |
| Acknowledgments | No TCP-style acknowledgments |
| Sequencing | No TCP-style sequencing |
| Retransmission | No TCP-style retransmission |
| Flow control | No TCP-style flow control |
| Higher overhead | Lower overhead |
| HTTPS, SSH, FTP, email | DNS queries, VoIP, streaming, gaming |

---

# TCP Three-Way Handshake

Before normal TCP data transfer, TCP establishes a connection using a three-way handshake.

## Step 1 – SYN

Client:

```text
SYN
```

Meaning:

> "I want to establish a connection."

The client sends an initial sequence number.

---

## Step 2 – SYN + ACK

Server:

```text
SYN + ACK
```

Meaning:

> "I received your request and I am ready to establish communication."

The server acknowledges the client's sequence number and provides its own initial sequence number.

---

## Step 3 – ACK

Client:

```text
ACK
```

Meaning:

> "I acknowledge your response."

The TCP connection is now established and data transfer can begin.

## Visual

```text
Client                         Server

SYN  -------------------------->

     <-------------------------- SYN + ACK

ACK  -------------------------->

          Connection Established
```

### Important Note

Initial sequence numbers are selected when the TCP connection is established. They are not simply assumed to start from zero.

---

# TCP Four-Way Termination

When a TCP connection is closed gracefully, the normal exchange is:

```text
FIN

↓

ACK

↓

FIN

↓

ACK
```

More precisely:

```text
Host A → Host B: FIN
Host B → Host A: ACK
Host B → Host A: FIN
Host A → Host B: ACK
```

This is commonly called the **TCP four-way handshake**.

---

# TCP Flags

TCP uses control flags to manage connections and data transfer.

| Flag | Purpose |
|---|---|
| SYN | Establish/synchronize a connection |
| ACK | Acknowledge received information |
| FIN | Gracefully terminate a connection |
| RST | Reset/abort a connection |
| PSH | Request data delivery to the application without waiting for additional buffering |
| URG | Indicates that the urgent pointer is significant |

The classic six flags covered here are:

```text
URG
ACK
PSH
RST
SYN
FIN
```

> Modern TCP headers can contain additional control flags beyond these six, but the six above are the traditional flags commonly introduced in networking fundamentals.

---

# 3. Internet Layer

The TCP/IP Internet Layer corresponds to the OSI Network Layer.

It is responsible for:

- Logical addressing
- Routing
- Packet forwarding
- Logical transmission of data across networks

## Main Protocols

- IPv4
- IPv6
- ICMP
- ARP

### IPv4 and IPv6

IP is responsible for delivering packets from a source host toward a destination host using IP addressing.

The two major versions are:

- IPv4
- IPv6

### ICMP

**ICMP (Internet Control Message Protocol)** is used to communicate network and error information.

Examples include messages used by:

- `ping`
- `traceroute`/`tracert`
- Network error reporting

### ARP

**ARP (Address Resolution Protocol)** maps a known IPv4 address to a MAC address on a local network.

ARP is sometimes classified differently depending on the TCP/IP/OSI mapping being used. It is commonly discussed with the Internet/Network layer because it resolves an IP address to a link-layer address, while its actual operation depends on Layer 2 networking.

ARP-related mechanisms can include:

- Reverse ARP
- Proxy ARP
- Gratuitous ARP
- Inverse ARP

## Devices

- Router
- Firewall
- Layer 3 Switch

## Address Used

IP address.

Example:

```text
192.168.10.50
```

---

# 4. Network Access Layer

The Network Access Layer combines the functions associated with:

- OSI Data Link Layer
- OSI Physical Layer

It is also referred to in some resources as the **Host-to-Network Layer**.

## Responsibilities

- Hardware/MAC addressing
- Frame forwarding
- Local network delivery
- Physical transmission
- Access to the physical network medium

## Protocols / Technologies

- Ethernet
- Wi-Fi
- PPP

## Devices

- Switch
- Hub
- Access Point
- Network interface hardware

---

# Encapsulation

Suppose you open:

```text
https://www.google.com
```

Data moves down the protocol stack.

## Application Layer

The application generates application data such as an HTTP request.

↓

## Transport Layer

TCP adds transport information such as:

- Source port
- Destination port
- Sequence number
- Acknowledgment information
- TCP control information

The result is a **TCP segment**.

↓

## Internet Layer

IP adds:

- Source IP
- Destination IP

The result is an **IP packet**.

↓

## Network Access Layer

The local network technology adds information such as:

- Source MAC
- Destination MAC

The result is an **Ethernet frame** when Ethernet is used.

↓

## Physical Transmission

The frame is transmitted as physical signals representing:

```text
101010101010
```

---

# Decapsulation

At the receiving system, the process happens in reverse:

```text
Bits
  ↓
Frame
  ↓
Packet
  ↓
Segment
  ↓
Data
```

Each protocol layer processes and removes the information associated with its layer until the application receives the original data.

---

# Complete Packet Flow

Suppose your laptop opens Google:

```text
Browser
   ↓
DNS Query
   ↓
TCP Handshake
   ↓
HTTPS Request
   ↓
Switch
   ↓
Firewall
   ↓
Router
   ↓
ISP
   ↓
Internet
   ↓
Google Server
   ↓
Response
   ↓
Browser Displays Page
```

The actual path can be more complex, and the exact devices involved depend on the network design.

---

# TCP Header Format

A TCP segment contains a TCP header followed by application data.

The TCP header is typically:

- **20 bytes minimum**
- **60 bytes maximum** when options are present

## Major TCP Header Fields

### 1. Source Port

Identifies the sending application/service endpoint.

### 2. Destination Port

Identifies the receiving application/service endpoint.

For example:

```text
Source Port: 51532
Destination Port: 443
```

The client commonly uses an **ephemeral source port**, while the server listens on a well-known or registered service port.

### Ephemeral Ports

A commonly used ephemeral range is:

```text
49152 – 65535
```

The exact selection mechanism and usable range can depend on the operating system and implementation.

---

### 3. Sequence Number

The sequence number is used to track the position of bytes in the TCP byte stream.

It allows TCP to:

- Maintain ordering
- Detect missing data
- Reassemble data correctly

---

### 4. Acknowledgment Number

When the ACK flag is set, the acknowledgment number identifies the next sequence number expected.

For example, if a receiver has successfully received data through sequence position `x`, it can acknowledge the next expected byte.

---

### 5. Header Length / Data Offset

The TCP header length is represented using a 4-bit field.

The maximum value corresponds to a 60-byte TCP header.

---

### 6. Reserved

Reserved bits are intended for future use and are normally set according to the protocol specification.

---

### 7. Flags

The flags control TCP connection behavior.

```text
URG
ACK
PSH
RST
SYN
FIN
```

---

### 8. Window Size

The TCP window field supports **flow control**.

It indicates how much additional data the receiver is currently prepared to accept.

This prevents the sender from overwhelming the receiving host's available receive buffer.

---

### 9. Checksum

The TCP checksum helps detect corruption in the TCP segment.

The checksum calculation includes:

```text
TCP Header
+
TCP Data
+
TCP Pseudo Header
```

---

# TCP Pseudo Header

The TCP pseudo header is used as part of checksum calculation.

It is **not transmitted as a separate TCP header**.

The traditional IPv4 pseudo header contains:

- Source IP address
- Destination IP address
- Reserved/fixed field
- Protocol
- TCP segment length

Its purpose is to help detect certain addressing and protocol errors during checksum validation.

---

# 10. Urgent Pointer

The urgent pointer is meaningful when the **URG** flag is set.

It identifies the location of urgent data relative to the TCP sequence space.

---

# 11. Options

TCP options provide additional functionality.

Examples can include mechanisms related to:

- Maximum Segment Size (MSS)
- Window scaling
- Selective acknowledgments
- Timestamps

The options field is variable in length and is padded as required to maintain the required header alignment.

---

# TCP Flow and Error Control

TCP provides mechanisms that make transport reliable.

## Sequencing

TCP numbers bytes in the data stream.

This allows the receiving side to place data in the correct order.

---

## Acknowledgment

The receiver acknowledges received data.

This allows the sender to determine what data has been successfully received.

---

## Retransmission

If TCP determines that data has not been successfully delivered, it can retransmit the missing data.

---

## Flow Control

TCP uses a receive window so the receiver can advertise how much data it can accept.

Conceptually:

```text
Sender
  │
  │ Data
  ↓
Receiver
  │
  │ Receive Window
  ↓
Sender
```

The sender uses the advertised receive window to avoid overwhelming the receiver.

---

## Error Detection

TCP uses a checksum to detect corruption in the segment.

Conceptually:

```text
TCP Checksum
=
TCP Header
+
TCP Data
+
Pseudo Header
```

---

# TCP Full-Duplex Communication

TCP supports **full-duplex communication**.

This means both endpoints can send and receive data at the same time.

For example:

```text
Client  ───────────────► Server
Client  ◄─────────────── Server
```

Both directions can operate independently within the established connection.

---

# TCP Stream-Oriented Communication

TCP provides a **byte-stream service**.

The application writes a stream of bytes, and TCP handles segmentation and reliable delivery.

TCP does not preserve application message boundaries in the way a message-oriented protocol would.

---

# Working of TCP Three-Way Handshake

The connection is established through three steps.

### Step 1 – SYN

The client sends a segment with:

- SYN flag set
- Client initial sequence number

```text
Client ───── SYN ─────► Server
```

### Step 2 – SYN + ACK

The server responds with:

- SYN flag set
- ACK flag set
- Server initial sequence number
- Acknowledgment of the client's initial sequence number

```text
Client ◄── SYN + ACK ─── Server
```

### Step 3 – ACK

The client acknowledges the server's sequence number.

```text
Client ───── ACK ─────► Server
```

A full-duplex TCP connection can now be used for data transfer.

---

# Why Does TCP Connection Termination Need Four-Way Handshake?

TCP is full-duplex, meaning the two directions of communication can be closed independently.

The normal graceful termination process is:

```text
Client ───── FIN ─────► Server

Client ◄──── ACK ───── Server

Client ◄──── FIN ───── Server

Client ───── ACK ─────► Server
```

The first FIN indicates that one side has finished sending.

The ACK confirms that FIN.

The other side may still have data to send, so it can continue before sending its own FIN.

The final ACK confirms the second FIN.

Therefore, the four messages represent two independent directions being closed.

> In some circumstances, an ACK and FIN can be combined into one segment. The four-step sequence describes the normal conceptual termination process.

---

# Real Firewall Example

Suppose a FortiGate receives traffic:

```text
192.168.10.10
       ↓
8.8.8.8
```

A simplified FortiGate processing sequence can include:

1. Interface check
2. Routing lookup
3. Firewall policy matching
4. NAT processing, if configured
5. Security inspection, if configured
6. Session creation
7. Forwarding

The exact packet-processing path depends on the configuration and FortiGate features involved.

---

# Troubleshooting Example

A website is not opening.

## Step 1 – Check the Gateway

Can the client reach its default gateway?

```text
ping <default-gateway>
```

If not, investigate local connectivity, VLAN, IP addressing, ARP, switching, or routing.

---

## Step 2 – Test Internet Reachability

Try:

```text
ping 8.8.8.8
```

If this works, basic IP connectivity to that destination is available.

---

## Step 3 – Test DNS

Try resolving:

```text
google.com
```

If IP connectivity works but name resolution fails, investigate DNS.

---

## Step 4 – Test TCP 443

HTTPS normally uses:

```text
TCP 443
```

If TCP connectivity cannot be established, investigate:

- Firewall policies
- Routing
- NAT
- Server availability
- ACLs
- Security controls

---

# TCP/IP Troubleshooting Flow

A practical troubleshooting sequence can be:

```text
Physical / Link
      ↓
Local Network
      ↓
IP Addressing
      ↓
Default Gateway
      ↓
Routing
      ↓
DNS
      ↓
TCP Connectivity
      ↓
Application
```

The exact troubleshooting order can vary depending on the symptoms.

---

# Common Interview Questions

## Q1. Why does TCP use a three-way handshake?

**Answer:**

The three-way handshake establishes the TCP connection and synchronizes the initial sequence numbers in both directions before normal data transfer begins.

---

## Q2. Why is UDP faster than TCP?

**Answer:**

UDP has lower protocol overhead because it does not establish a TCP connection or provide TCP's acknowledgment, sequencing, retransmission, and flow-control mechanisms.

---

## Q3. Which applications commonly use TCP?

Examples:

- HTTPS
- SSH
- FTP
- Email protocols

---

## Q4. Which applications commonly use UDP?

Examples:

- DNS queries
- VoIP
- IPTV
- Gaming
- Real-time media

> Protocol selection depends on the application and implementation. Some applications can use either TCP or UDP depending on their design.

---

## Q5. What happens if the SYN-ACK is lost?

Example:

```text
Client ───── SYN ─────► Server

Client ◄── SYN-ACK ─── Server
              X
            Lost

Client waits / retransmits
```

The TCP connection is not fully established until the handshake successfully completes.

---

## Q6. What is the difference between TCP and UDP?

**TCP** provides a reliable, connection-oriented byte-stream service.

**UDP** provides a lightweight, connectionless datagram service without TCP's reliability mechanisms.

---

## Q7. What is a TCP segment?

A TCP segment consists of:

```text
TCP Header + TCP Data
```

It is the Transport Layer PDU when TCP is used.

---

## Q8. What is an IP packet?

An IP packet consists of:

```text
IP Header + IP Payload
```

The payload can contain a TCP segment, UDP datagram, ICMP message, or another supported protocol payload.

---

## Q9. What is a TCP port?

A TCP port identifies a transport-layer endpoint associated with an application or service.

Examples:

```text
HTTPS → 443
SSH   → 22
HTTP  → 80
```

---

## Q10. Why do we need both TCP and HTTPS?

They solve different problems:

```text
TCP
↓
Reliable transport

TLS / HTTPS
↓
Security, encryption, authentication, integrity
```

TCP provides reliable transport.

HTTPS uses HTTP over TLS to provide secure application communication.

---

# OSI vs TCP/IP Interview Question

### Question

> Which model is actually used in real networks?

### Answer

The **TCP/IP protocol suite** is the practical foundation of modern IP networks and the Internet.

The **OSI model** is primarily a conceptual/reference model that is extremely useful for understanding networking and troubleshooting.

---

# Interview Answer (3–4 Minutes)

> **"The TCP/IP model is the practical networking model used on the Internet. It consists of four layers: Application, Transport, Internet, and Network Access. The Application layer provides services such as HTTP, DNS, and FTP. The Transport layer uses TCP for reliable communication and UDP for lightweight communication. The Internet layer handles logical addressing and routing using IP, while the Network Access layer handles local network delivery, MAC addressing, and physical transmission. During communication, data is encapsulated as it moves down the layers and decapsulated at the receiving end."**

---

# Memory Trick

## TCP

> **Reliable, connection-oriented, acknowledgment-based.**

## UDP

> **Fast, connectionless, no TCP-style guarantee of delivery.**

---

# ⭐ Gold Interview Tip

Interviewers often ask:

> **"If TCP is reliable, why do we still need applications like HTTPS?"**

A strong answer is:

- **TCP provides reliable transport**
- **TLS provides encryption and authentication**
- **HTTPS provides secure HTTP communication**

They solve different problems:

| Technology | Primary Role |
|---|---|
| TCP | Reliable transport |
| IP | Logical addressing and routing |
| TLS | Encryption, authentication, integrity |
| HTTPS | Secure HTTP communication |

---

# Quick Revision Notes

- **TCP/IP** = Practical protocol suite used by modern IP networks.
- **Application Layer** = OSI 5–7 functions.
- **Transport Layer** = End-to-end transport using TCP/UDP.
- **Internet Layer** = IP addressing and routing.
- **Network Access Layer** = Local delivery and physical transmission.
- **TCP** = Reliable, connection-oriented, stream-oriented.
- **UDP** = Connectionless, lightweight datagram transport.
- **TCP 3-way handshake** = SYN → SYN-ACK → ACK.
- **TCP termination** = FIN → ACK → FIN → ACK.
- **TCP segment** = TCP header + data.
- **IP packet** = IP header + payload.
- **TCP port** = Identifies a transport-layer endpoint.
- **Encapsulation** = Headers/control information are added as data moves down the stack.
- **Decapsulation** = Protocol information is processed/removed as data moves up the stack.
- **TCP window** = Flow control.
- **TCP checksum** = Error detection.
- **IP** = Logical addressing and routing.
- **ICMP** = Network/control and error reporting.
- **ARP** = IPv4 address-to-MAC resolution on a local network.

---

# 🎯 Assignment – Interview Style

Try answering these without looking back:

1. What is the TCP/IP model?
2. What are the four TCP/IP layers?
3. How does the TCP/IP model map to the OSI model?
4. What is the difference between TCP and UDP?
5. Explain the TCP three-way handshake.
6. Explain the TCP four-way termination process.
7. Why does TCP need sequence numbers?
8. What is TCP flow control?
9. What is the purpose of the TCP checksum?
10. What is the difference between a TCP segment and an IP packet?
11. What is an ephemeral port?
12. What happens when a SYN-ACK is lost?
13. Why can TCP termination require four messages?
14. What is the role of the Internet Layer?
15. What is the role of the Network Access Layer?
16. Explain encapsulation and decapsulation.
17. A user can ping `8.8.8.8` but cannot open `google.com`. What would you investigate?
18. A user cannot establish TCP 443 connectivity. What network/security components would you check?
19. Why do we need HTTPS if TCP is already reliable?
20. Explain the TCP/IP model in 3–4 minutes as if you were in a networking interview.

---

# 📚 Further Learning / Reference Topics

The following topics are natural next steps after understanding the fundamentals in this chapter:

- TCP congestion control
- TCP error control
- TCP connection termination
- TCP/IP interview questions
- TCP header analysis in Wireshark
- TCP retransmissions
- TCP windowing
- TCP MSS and MTU
- TCP Selective Acknowledgment
- TCP connection states

### Reference Material

- [Firewall.cx – TCP](https://www.firewall.cx/networking-topics/protocols/tcp.html)
- [GeeksforGeeks – Why TCP Connection Termination Needs a 4-Way Handshake](https://www.geeksforgeeks.org/why-tcp-connect-termination-need-4-way-handshake/)
- [GeeksforGeeks – TCP Congestion Control](https://www.geeksforgeeks.org/tcp-congestion-control/)
- [GeeksforGeeks – Error Control in TCP](https://www.geeksforgeeks.org/error-control-in-tcp/)
- [GeeksforGeeks – What is Transmission Control Protocol (TCP)?](https://www.geeksforgeeks.org/what-is-transmission-control-protocol-tcp/)
- [GeeksforGeeks – Top TCP/IP Interview Questions](https://www.geeksforgeeks.org/top-50-tcp-ip-interview-questions-and-answers/)
- [JavaTpoint – TCP](https://www.javatpoint.com/tcp)

---

# 🚀 Chapter Summary

The TCP/IP model explains how modern network communication works from application data down to physical transmission.

The key concepts to remember are:

```text
Application
     ↓
Transport
     ↓
Internet
     ↓
Network Access
```

And for TCP:

```text
SYN
  ↓
SYN + ACK
  ↓
ACK
  ↓
Data Transfer
  ↓
FIN
  ↓
ACK
  ↓
FIN
  ↓
ACK
```

Understanding these concepts provides the foundation for later topics such as **routing, firewalls, NAT, VPNs, packet captures, FortiGate troubleshooting, and network security**.
