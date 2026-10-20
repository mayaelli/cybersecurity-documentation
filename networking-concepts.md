 # Networking Fundamentals: OSI, TCP/IP, IP Addressing, TCP, UDP, and Encapsulation

## Overview

This module introduced the foundational concepts of computer networking, focusing on how devices communicate across networks and the Internet. Key topics covered include the OSI model, TCP/IP model, IP addressing, transport protocols (TCP and UDP), packet flow, and encapsulation.

One realization from this module is that networking is essentially a series of layers working together. Every layer performs a specific task and then passes the data to the next layer. Instead of one protocol doing everything, the responsibility is divided among multiple layers.

---

# OSI Model

The OSI (Open Systems Interconnection) Model is a conceptual framework that explains how data travels from one device to another through seven layers.

| Layer | Function |
|---------|---------|
| 7. Application | User-facing services such as HTTP, DNS, SMTP |
| 6. Presentation | Data formatting, encryption, compression |
| 5. Session | Establishes and manages sessions |
| 4. Transport | Reliable or unreliable delivery (TCP/UDP) |
| 3. Network | Logical addressing and routing (IP) |
| 2. Data Link | Physical addressing (MAC), framing |
| 1. Physical | Electrical, wireless, and physical transmission |

## Simple Memory Trick

```text
Application
Presentation
Session
Transport
Network
Data Link
Physical
```

Data travels downward when sending and upward when receiving.

---

# TCP/IP Model

In real-world networking, the TCP/IP model is used more commonly than the OSI model.

| TCP/IP Layer | Corresponding OSI Layers |
|-------------|-------------------------|
| Application | Application, Presentation, Session |
| Transport | Transport |
| Internet | Network |
| Network Access | Data Link + Physical |

## Data Flow

```text
Application
    ↓
Transport
    ↓
Internet
    ↓
Network Access
```

---

# IP Addressing

An IP address uniquely identifies a device on a network.

Example:

```text
192.168.100.10
```

An IP address answers the question:

> Which device should receive this data?

## My Realization

A useful way to think about networking is:

```text
IP Address = House Address
Port Number = Room Number
```

If a device is a house, the IP address finds the correct house while the port number finds the correct room inside the house.

---

# Ports

A port identifies a specific process or application running on a device.

Examples:

```text
80     = HTTP
443    = HTTPS
53     = DNS
3389   = RDP
```

Port numbers range from:

```text
1 - 65535
```

Port 0 is reserved.

## My Realization

IP addresses identify computers.

Ports identify applications.

Without ports, the operating system would not know which process should receive incoming data.

---

# UDP (User Datagram Protocol)

UDP is a connectionless transport protocol.

Characteristics:

- Fast
- Lightweight
- No connection setup
- No delivery confirmation
- No retransmission

## Packet Flow

```text
Sender
   ↓
Receiver
```

That's it.

UDP sends packets without checking whether they arrived successfully.

## Common Uses

- Online gaming
- Voice calls
- Live streaming
- Video conferencing

## Real-Life Analogy

UDP is like sending a letter through regular mail.

You send it and hope it arrives.

There is no confirmation.

## My Realization

UDP sacrifices reliability for speed.

In applications like voice calls, missing one packet is usually better than waiting for retransmission.

---

# TCP (Transmission Control Protocol)

TCP is a connection-oriented transport protocol.

Characteristics:

- Reliable
- Uses acknowledgements
- Detects lost data
- Retransmits lost packets
- Preserves order

## TCP Connection

Before sending data, TCP establishes a connection.

This process is called the Three-Way Handshake.

### Step 1 - SYN

Client:

```text
Can we communicate?
```

### Step 2 - SYN-ACK

Server:

```text
Yes.
Can you hear me too?
```

### Step 3 - ACK

Client:

```text
Yes.
Let's start.
```

Connection established.

---

# Sequence Numbers

TCP assigns sequence numbers to transmitted data.

Example:

```text
1
2
3
4
5
```

If the receiver gets:

```text
1
2
4
5
```

It immediately detects that packet 3 is missing and requests retransmission.

## My Realization

TCP is less about speed and more about accuracy.

It constantly checks:

```text
Did you receive this?
```

while UDP simply says:

```text
Good luck.
```

---

# Encapsulation

Encapsulation is the process of adding protocol-specific headers (and sometimes trailers) as data moves down the networking stack.

Each layer adds information needed for its specific job.

## Data Transformation

### Application Layer

Original data:

```text
Hello
```

### Transport Layer

Adds TCP or UDP header:

```text
[TCP Header][Hello]
```

Result:

```text
TCP Segment
```

### Network Layer

Adds IP header:

```text
[IP Header][TCP Header][Hello]
```

Result:

```text
IP Packet
```

### Data Link Layer

Adds frame header and trailer:

```text
[Frame Header]
[IP Header]
[TCP Header]
[Hello]
[Frame Trailer]
```

Result:

```text
Frame
```

---

# Encapsulation Flow

```text
Data
 ↓
Segment
 ↓
Packet
 ↓
Frame
```

This was one of the biggest concepts from the module.

Every layer wraps the previous layer with additional information.

A useful analogy is a package being wrapped multiple times before shipping.

---

# Decapsulation

When the receiving device gets the frame, the process happens in reverse.

```text
Frame
 ↑
Packet
 ↑
Segment
 ↑
Data
```

## Receiving Process

### Data Link Layer

Removes:

```text
Frame Header
Frame Trailer
```

### Network Layer

Removes:

```text
IP Header
```

### Transport Layer

Removes:

```text
TCP Header
```

### Application Layer

Receives:

```text
Actual Data
```

---

# Packet Life Cycle

Example: Searching for a room on TryHackMe.

### Step 1

User enters:

```text
Active Directory
```

into the browser.

### Step 2

Browser creates an HTTP request.

### Step 3

TCP establishes a connection through the Three-Way Handshake.

### Step 4

TCP adds its header and creates a segment.

### Step 5

IP adds source and destination IP addresses and creates a packet.

### Step 6

Ethernet/WiFi adds frame information and creates a frame.

### Step 7

Routers forward the packet toward the destination network.

### Step 8

The destination device decapsulates the frame and passes the data upward through the layers.

### Step 9

The application receives and processes the request.

---

# Telnet HTTP Exercise

One practical exercise involved connecting directly to a web server using Telnet.

```bash
telnet <target-ip> 80
```

Sending an HTTP request manually:

```http
GET / HTTP/1.1
Host: telnet.thm
```

Server Response:

```http
HTTP/1.1 200 OK
```

Flag:

```text
THM{TELNET_MASTER}
```

## My Realization

This exercise demonstrated what browsers do behind the scenes.

A browser is essentially:

1. Creating HTTP requests
2. Sending them through TCP/IP
3. Receiving HTTP responses
4. Rendering the HTML

Using Telnet allowed me to communicate directly with the web server without any browser involved.

---

# Key Takeaways

- IP addresses identify devices.
- Port numbers identify applications.
- UDP prioritizes speed over reliability.
- TCP prioritizes reliability over speed.
- TCP uses the Three-Way Handshake to establish connections.
- Encapsulation wraps data as it moves down the network stack.
- Decapsulation unwraps data as it moves up the network stack.
- Frames operate at the Data Link Layer.
- Packets operate at the Network Layer.
- Segments operate at the Transport Layer.
- Every networking layer has a specific responsibility.
- Routers mainly make forwarding decisions using IP addresses.
- Browsers are simply applications that automate the process of sending and receiving HTTP requests.

---

# Favorite Realizations

> IP finds the house. Ports find the room.

> UDP says: "I sent it."

> TCP says: "Did you get it?"

> Encapsulation is just wrapping data in layers.

> Sending is wrapping. Receiving is unwrapping.

```text
Data → Segment → Packet → Frame
Frame → Packet → Segment → Data
```

These simple mental models made the entire networking stack much easier to understand.
