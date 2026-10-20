# Networking Essentials (TryHackMe) - Study Notes & Documentation

## Overview

This module expanded on foundational networking concepts by introducing the protocols and technologies that allow networks to function automatically and efficiently.

Topics covered:

- Dynamic Host Configuration Protocol (DHCP)
- Address Resolution Protocol (ARP)
- Internet Control Message Protocol (ICMP)
- Ping
- Traceroute
- Routing Protocols
- Network Address Translation (NAT)

One major realization from this room is that most networking happens automatically behind the scenes. Every time we connect to WiFi, browse the internet, or communicate with another device, multiple protocols work together without any user interaction.

---

# Dynamic Host Configuration Protocol (DHCP)

## Overview

DHCP automatically provides network configuration to devices joining a network.

Without DHCP, every device would need manual configuration.

Required network settings:

- IP Address
- Subnet Mask
- Default Gateway
- DNS Server

## Why DHCP Exists

Without DHCP:

```text
Join WiFi
↓
Manually enter IP Address
↓
Manually enter Gateway
↓
Manually enter DNS Server
↓
Access Network
```

With DHCP:

```text
Join WiFi
↓
DHCP does everything automatically
↓
Access Network
```

---

# DHCP Ports

| Device | Port |
|----------|----------|
| DHCP Server | UDP 67 |
| DHCP Client | UDP 68 |

DHCP uses UDP because the client does not yet have a valid IP address when communication begins.

---

# DHCP Process (DORA)

DHCP follows four steps known as DORA.

## 1. Discover

The client broadcasts:

```text
Any DHCP server available?
```

Packet:

```text
DHCPDISCOVER
```

---

## 2. Offer

The DHCP server replies:

```text
I can offer you this IP address.
```

Packet:

```text
DHCPOFFER
```

---

## 3. Request

The client responds:

```text
I would like that address.
```

Packet:

```text
DHCPREQUEST
```

---

## 4. Acknowledge

The server confirms:

```text
That IP address is now yours.
```

Packet:

```text
DHCPACK
```

---

# DORA Memory Trick

```text
D = Discover
O = Offer
R = Request
A = Acknowledge
```

---

# Important DHCP Addresses

Before receiving an IP address, the client uses:

Source IP:

```text
0.0.0.0
```

Destination IP:

```text
255.255.255.255
```

Reason:

The client does not yet know any network information.

---

# DHCP Broadcast MAC Address

```text
ff:ff:ff:ff:ff:ff
```

Meaning:

```text
Everyone on the network, listen!
```

---

# Information Received from DHCP

After successful configuration, the client receives:

✅ IP Address

✅ Subnet Mask

✅ Default Gateway

✅ DNS Server

---

# Address Resolution Protocol (ARP)

## Overview

ARP connects Layer 3 and Layer 2 addressing.

It answers the question:

> Which MAC address belongs to this IP address?

---

# Why ARP Is Needed

Before sending an Ethernet frame, a device must know:

```text
Destination MAC Address
```

However, users and applications typically know only the IP address.

ARP performs the translation.

```text
IP Address
↓
ARP
↓
MAC Address
```

---

# ARP Request

Example:

```text
Who has 192.168.66.1?
Tell 192.168.66.89
```

This request is broadcast to every device on the local network.

Destination MAC:

```text
ff:ff:ff:ff:ff:ff
```

---

# ARP Reply

The owner of the IP address replies:

```text
192.168.66.1 is at
44:df:65:d8:fe:6c
```

Now communication can proceed.

---

# ARP Process

## Step 1

ARP Request

```text
Who has this IP?
```

---

## Step 2

ARP Reply

```text
I own that IP.
Here's my MAC address.
```

---

# Ethernet Frame Reminder

An Ethernet frame contains:

- Destination MAC Address
- Source MAC Address
- Type Field

Example:

```text
[Destination MAC]
[Source MAC]
[Type]
[Payload]
```

---

# Important ARP Facts

- ARP operates within local networks.
- ARP messages are encapsulated directly inside Ethernet frames.
- ARP is not carried inside TCP.
- ARP is not carried inside UDP.
- ARP is not carried inside IP packets.

---

# My Realization

IP addresses tell us:

```text
Who?
```

MAC addresses tell us:

```text
Exactly where on the local network.
```

ARP is basically the network's contact directory.

---

# Internet Control Message Protocol (ICMP)

## Overview

ICMP handles diagnostics and error reporting.

It helps determine:

- Is the destination reachable?
- Is the destination alive?
- Where did a packet fail?

---

# Common ICMP Uses

## Ping

Tests connectivity.

## Traceroute

Reveals the path packets take.

---

# Ping

## Purpose

Determine whether a destination is alive and reachable.

Command:

```bash
ping 8.8.8.8
```

---

# How Ping Works

Your computer sends:

```text
ICMP Echo Request
Type 8
```

Meaning:

```text
Hey, are you there?
```

---

Target responds:

```text
ICMP Echo Reply
Type 0
```

Meaning:

```text
Yes, I'm here.
```

---

# Round Trip Time (RTT)

RTT measures:

```text
Computer
↓
Destination
↓
Computer
```

The complete trip.

Measured in:

```text
Milliseconds (ms)
```

---

# Packet Loss

Example:

```text
4 transmitted
4 received
0% packet loss
```

Means:

```text
No packets were lost.
```

---

Packet loss may occur because of:

- Network congestion
- Firewall filtering
- Poor connectivity
- Offline systems

---

# Ping Cheat Sheet

| ICMP Type | Meaning |
|------------|------------|
| 8 | Echo Request |
| 0 | Echo Reply |

---

# Traceroute

## Purpose

Discover every router between you and the destination.

Linux:

```bash
traceroute example.com
```

Windows:

```cmd
tracert example.com
```

---

# Time To Live (TTL)

Every IP packet contains a field called:

```text
TTL
```

Time To Live

---

## What TTL Does

Every router decreases TTL by one.

Example:

```text
TTL = 4

Router 1 → TTL = 3
Router 2 → TTL = 2
Router 3 → TTL = 1
Router 4 → TTL = 0
```

Packet destroyed.

---

# ICMP Time Exceeded

When TTL reaches zero:

Router sends:

```text
ICMP Type 11
```

Known as:

```text
Time Exceeded
```

---

# How Traceroute Works

Traceroute deliberately manipulates TTL.

Example:

```text
TTL = 1
```

Router 1 drops packet and identifies itself.

---

```text
TTL = 2
```

Router 2 reveals itself.

---

```text
TTL = 3
```

Router 3 reveals itself.

---

Process continues until destination is reached.

---

# Traceroute Findings

Traceroute can reveal:

- Routers
- ISPs
- Geographic locations
- Network paths

---

# Why Some Hops Show *

Example:

```text
* * *
```

Means:

- Router ignored the request
- ICMP replies are blocked
- Router is configured not to respond

Not necessarily a failure.

---

# ICMP Cheat Sheet

| Type | Purpose |
|---------|---------|
| 0 | Echo Reply |
| 8 | Echo Request |
| 11 | Time Exceeded |

---

# Routing

## Overview

Routers need to determine the best path for data to travel.

Routing protocols help automate these decisions.

---

# OSPF

Open Shortest Path First

Purpose:

```text
Find shortest path through a network.
```

Commonly used in enterprise networks.

---

# EIGRP

Enhanced Interior Gateway Routing Protocol

Characteristics:

- Cisco proprietary
- Uses multiple metrics
- Finds efficient routes

---

# RIP

Routing Information Protocol

Characteristics:

- Older protocol
- Uses hop count
- Simpler but less efficient

---

# BGP

Border Gateway Protocol

The most important routing protocol on the Internet.

Used by:

- ISPs
- Large organizations
- Internet backbone providers

---

# My Realization

Without routing protocols:

```text
Routers wouldn't know
where to send packets.
```

The Internet would not function.

---

# Network Address Translation (NAT)

## Overview

NAT allows multiple devices to share a single public IP address.

This helps conserve IPv4 addresses.

---

# Why NAT Exists

Without NAT:

```text
Every device
=
One Public IP
```

Not sustainable.

---

With NAT:

```text
50 devices
↓
1 Public IP
↓
Internet
```

---

# NAT Translation Example

Internal Device:

```text
192.168.0.129
Port 15401
```

Gets translated to:

```text
212.3.4.5
Port 19273
```

To the Internet, the connection appears to originate from:

```text
212.3.4.5
```

---

# NAT Table

Router keeps a translation table:

```text
Internal IP:Port
↓
External IP:Port
```

This allows responses to return to the correct local device.

---

# My Realization

Every device in my house appears to websites as:

```text
One Public IP
```

Even though many devices are connected.

The router acts like a receptionist keeping track of who started which conversation.

---

# Relationship Between Key Protocols

```text
DHCP
↓
Gives me an IP address

ARP
↓
Finds MAC addresses

ICMP
↓
Tests connectivity

Routing
↓
Finds best path

NAT
↓
Shares one public IP
```

Every protocol solves a different networking problem.

---

# Key Takeaways

- DHCP automatically assigns IP configuration.
- DHCP uses the DORA process.
- DHCP uses UDP ports 67 and 68.
- ARP maps IP addresses to MAC addresses.
- ARP requests use the broadcast MAC address.
- ICMP provides diagnostics and error reporting.
- Ping uses ICMP Echo Requests and Replies.
- Traceroute uses TTL and ICMP Time Exceeded messages.
- Routing protocols help routers find efficient paths.
- NAT allows multiple devices to share one public IPv4 address.
- Most networking processes happen automatically behind the scenes.

---

# Favorite Realizations

> DHCP = "Who am I on this network?"

> ARP = "Who owns this IP address?"

> ICMP = "Are you alive?"

> Traceroute = "Who did my packet talk to on the way?"

> Routing = "Which path should I take?"

> NAT = "How can 50 devices pretend to be one?"

Networking is essentially devices constantly asking and answering questions until communication becomes possible.
