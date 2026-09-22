# Computer Networks — Complete Notes (OA Level)

## 1. Basics
- **Bandwidth** — max capacity of a channel (bits/sec).
- **Throughput** — actual achieved rate.
- **Latency (Delay)** = Propagation delay + Transmission delay + Queuing delay + Processing delay.
  - Propagation delay = Distance / Propagation speed.
  - Transmission delay = Packet size / Bandwidth.
- **Bandwidth-Delay Product** = Bandwidth × RTT (amount of data "in flight").

## 2. OSI Model (7 Layers) — Full Detail
| # | Layer | PDU | Function | Protocols/Devices |
|---|---|---|---|---|
| 7 | Application | Data | Network process to application, user interface | HTTP, FTP, SMTP, DNS, Telnet |
| 6 | Presentation | Data | Translation, encryption, compression | SSL/TLS, JPEG, ASCII |
| 5 | Session | Data | Establishes, manages, terminates sessions | NetBIOS, RPC, PPTP |
| 4 | Transport | Segment | End-to-end connections, reliability, flow control | TCP, UDP |
| 3 | Network | Packet | Logical addressing, routing, path determination | IP, ICMP, Routers |
| 2 | Data Link | Frame | Physical addressing (MAC), error detection, framing | Ethernet, ARP, Switches, Bridges |
| 1 | Physical | Bit | Transmission of raw bits over medium | Cables, Hubs, Repeaters |

## 3. TCP/IP Model (4 Layers)
Application → Transport → Internet → Network Access (Link)
- Combines OSI's top 3 into "Application"; combines Data Link + Physical into "Network Access."

## 4. Transport Layer (Detailed)

### TCP (Transmission Control Protocol)
- Connection-oriented, reliable, ordered, byte-stream, full-duplex.
- **3-Way Handshake**: SYN → SYN-ACK → ACK.
- **4-Way Termination**: FIN → ACK → FIN → ACK (or combined FIN-ACK).
- **Sequence & Acknowledgment numbers** — ensure ordering & reliability.
- **Flow Control** — Sliding Window Protocol (receiver advertises window size).
- **Congestion Control** — TCP dynamically adjusts sending rate:
  - **Slow Start** — congestion window (cwnd) starts small, doubles every RTT (exponential growth) until threshold (ssthresh).
  - **Congestion Avoidance** — after threshold, cwnd grows linearly (additive increase).
  - **Fast Retransmit** — on 3 duplicate ACKs, retransmit lost segment immediately (don't wait for timeout).
  - **Fast Recovery** — after fast retransmit, cwnd reduced (not reset to 1), continues in congestion avoidance.
  - On timeout: cwnd reset to 1, slow start restarts, ssthresh = cwnd/2.
  - **TCP Tahoe** — always resets cwnd to 1 on loss.
  - **TCP Reno** — uses fast recovery, doesn't fully reset on 3-dup-ACK loss.

### UDP (User Datagram Protocol)
- Connectionless, unreliable, unordered, low overhead, faster.
- Header only 8 bytes (Source port, Dest port, Length, Checksum) vs TCP's 20+ bytes.
- Used for: DNS, DHCP, streaming, VoIP, online gaming.

### Sockets & Ports
- **Socket** = IP address + Port number.
- **Well-known ports**: 0–1023 (HTTP:80, HTTPS:443, FTP:20/21, SSH:22, Telnet:23, SMTP:25, DNS:53, POP3:110, IMAP:143).
- **Registered ports**: 1024–49151.
- **Dynamic/Private ports**: 49152–65535.

## 5. Network Layer (Detailed)

### IP Addressing
- **IPv4** — 32-bit, dotted decimal (e.g. 192.168.1.1).
- **IPv6** — 128-bit, hexadecimal colon notation, solves address exhaustion, has built-in security (IPSec), no need for NAT/broadcast (uses multicast).

### IPv4 Classes
| Class | Range | Default Mask | Use |
|---|---|---|---|
| A | 0–127 | /8 | Large networks |
| B | 128–191 | /16 | Medium networks |
| C | 192–223 | /24 | Small networks |
| D | 224–239 | — | Multicast |
| E | 240–255 | — | Experimental |

### Subnetting (Numerical Skill — Practice This!)
- Given IP + subnet mask → find: Network Address, Broadcast Address, Number of hosts, Usable host range.
- **Number of hosts** = 2^(32 − prefix) − 2 (subtract network + broadcast address).
- **CIDR notation** — e.g. 192.168.1.0/24 means first 24 bits are network bits.
- Example: /26 mask (255.255.255.192) → 2^6=64 addresses per subnet, 62 usable hosts.

### NAT (Network Address Translation)
Translates private IP ↔ public IP, allows multiple devices to share one public IP, adds security layer (hides internal structure).

### Routing Algorithms
- **Distance Vector Routing** (e.g. RIP) — Bellman-Ford based; each router shares its FULL routing table with directly connected neighbors periodically; slow convergence; **Count-to-infinity problem** (solved by split horizon, route poisoning).
- **Link State Routing** (e.g. OSPF) — Dijkstra's algorithm based; each router builds complete network topology map by flooding link-state info to ALL routers; faster convergence, more overhead/memory.
- **Path Vector Routing** (e.g. BGP) — used between autonomous systems (Internet backbone).

### Dijkstra's Algorithm (know this cold — frequently in OA)
Find shortest path from source to all nodes: maintain distance array, repeatedly pick unvisited node with min distance, relax its neighbors' distances. O(V²) with array, O(E log V) with min-heap.

## 6. Data Link Layer (Detailed)

### Framing
Divides bit stream into manageable frames — methods: character count, byte stuffing (flag bytes with escape sequences), bit stuffing (insert 0 after five consecutive 1s to avoid confusion with flag pattern).

### Error Detection & Correction
- **Parity Bit** — single bit added; detects odd number of bit errors only, can't correct.
- **Checksum** — sum of data segments (1's complement); used in TCP/IP/UDP headers.
- **CRC (Cyclic Redundancy Check)** — polynomial division; appended remainder; very strong detection, used in Ethernet; can't correct, only detect.
- **Hamming Code** — adds redundant bits at positions that are powers of 2; can detect AND correct single-bit errors. Formula: 2^r ≥ m + r + 1 (r = redundant bits, m = data bits).

### Flow Control Protocols
- **Stop-and-Wait** — sender sends one frame, waits for ACK before sending next; simple but slow (low utilization).
- **Sliding Window** — sender can send multiple frames (window size) before needing ACK; improves throughput.
- **Go-Back-N ARQ** — sender can send N frames; on error, retransmits ALL frames from the erroneous one onward (receiver discards out-of-order frames).
- **Selective Repeat ARQ** — only retransmits the specific erroneous frame (receiver buffers out-of-order frames); more efficient but more complex (needs larger buffer).

### Multiple Access Protocols
- **ALOHA** — Pure ALOHA (transmit anytime, high collision, max efficiency ~18.4%); Slotted ALOHA (transmit only at slot boundaries, max efficiency ~36.8%).
- **CSMA (Carrier Sense Multiple Access)** — sense channel before transmitting.
  - **CSMA/CD (Collision Detection)** — used in wired Ethernet; detects collision during transmission, stops & sends jam signal, backs off randomly (binary exponential backoff).
  - **CSMA/CA (Collision Avoidance)** — used in wireless (WiFi); can't reliably detect collisions, so avoids them (RTS/CTS handshake, random backoff before sending).
- **Token Passing** — a "token" circulates; only holder can transmit (Token Ring) — no collisions but token overhead/single point of failure.

### Data Link Protocols
- **HDLC (High-Level Data Link Control)** — bit-oriented protocol.
- **PPP (Point-to-Point Protocol)** — used for direct connections (e.g. dial-up, DSL).

## 7. Physical Layer Basics
- Transmission media: Guided (Twisted pair, Coaxial, Fiber optic) vs Unguided (Radio, Microwave, Infrared).
- Fiber optic — highest bandwidth, immune to EM interference, most expensive.
- Modulation techniques: Amplitude, Frequency, Phase modulation (analog); Manchester encoding (digital).

## 8. Application Layer Protocols (Detailed)

### HTTP
- Stateless, request-response protocol.
- **Methods**: GET (retrieve), POST (submit/create), PUT (update/replace), PATCH (partial update), DELETE (remove), HEAD (headers only), OPTIONS.
- **Status Codes**: 1xx Informational, 2xx Success (200 OK, 201 Created), 3xx Redirection (301 Moved, 304 Not Modified), 4xx Client Error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found), 5xx Server Error (500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable).
- **HTTP/1.1** — persistent connections; **HTTP/2** — multiplexing, header compression; **HTTPS** — HTTP over TLS/SSL (port 443).
- **Cookies** — client-side storage for session state (HTTP is stateless, cookies add statefulness); **Sessions** — server-side state tracking, usually referenced via a session ID cookie.

### DNS (Domain Name System)
Resolves domain name → IP address. Hierarchical: Root → TLD (.com, .org) → Authoritative server.
Resolution order: Browser cache → OS cache → Router cache → ISP resolver → Root → TLD → Authoritative → back to client.
Record types: A (IPv4), AAAA (IPv6), CNAME (alias), MX (mail server), NS (name server).

### DHCP (Dynamic Host Configuration Protocol)
Auto-assigns IP address to devices on network. Process: **DORA** — Discover, Offer, Request, Acknowledge.

### Email Protocols
- **SMTP** — sends mail (client→server, server→server).
- **POP3** — retrieves mail, typically downloads & deletes from server.
- **IMAP** — retrieves mail, keeps synced with server (supports folders, partial download).

### FTP
Transfers files; uses 2 connections — control (port 21) and data (port 20).

## 9. Network Security Basics
- **Symmetric Encryption** — same key for encrypt/decrypt (fast, e.g. AES); key distribution problem.
- **Asymmetric Encryption** — public/private key pair (e.g. RSA); solves key distribution but slower.
- **SSL/TLS** — provides encryption, authentication, integrity for data in transit (HTTPS uses this).
- **Firewall** — filters traffic based on rules (packet filtering, stateful inspection, proxy).
- **VPN** — creates encrypted tunnel over public network for secure private communication.
- **Digital Signature** — ensures authenticity & non-repudiation, uses sender's private key to sign, receiver verifies with public key.

## 10. Switching & Routing Devices
| Device | OSI Layer | Function |
|---|---|---|
| Repeater | 1 | Regenerates signal |
| Hub | 1 | Broadcasts to all ports (no filtering) |
| Bridge | 2 | Connects 2 LAN segments, filters by MAC |
| Switch | 2 | Multi-port bridge, forwards based on MAC table |
| Router | 3 | Connects different networks, forwards based on IP |
| Gateway | All (esp. 7) | Connects networks with different protocols |

## 11. Switching Techniques
- **Circuit Switching** — dedicated path, reserved bandwidth, no data loss but wasteful if idle (e.g. traditional telephone).
- **Packet Switching** — data broken into packets, routed independently, more efficient bandwidth use (used by Internet).
  - **Datagram approach** — each packet routed independently (connectionless, e.g. IP).
  - **Virtual Circuit approach** — path established first, all packets follow it (connection-oriented, e.g. ATM, MPLS).
- **Message Switching** — entire message stored & forwarded hop-by-hop (old, e.g. telegraph systems).

## 12. Common Numericals in OA
- Transmission delay = Packet size (bits) / Bandwidth (bps).
- Propagation delay = Distance (m) / Propagation speed (m/s, ~2×10^8 for copper/fiber).
- Total time (Stop-and-Wait) = Transmission time + 2×Propagation delay + Processing/ACK time.
- Throughput with Stop-and-Wait efficiency = 1 / (1 + 2a), where a = Propagation delay / Transmission delay.
- Number of bits needed for sequence numbers in Sliding Window = based on window size (Go-Back-N: window ≤ 2^n − 1; Selective Repeat: window ≤ 2^(n-1)).
- Subnetting: given IP/prefix, compute network ID (AND operation), broadcast ID (OR with inverted mask), valid host range, number of subnets/hosts.

## 13. Common Traps / OA Gotchas
- TCP handshake is 3-way; termination is 4-way (because FIN doesn't always come with ACK immediately — can be combined into 3 if piggybacked).
- UDP has NO handshake, NO retransmission, NO flow/congestion control by itself.
- CSMA/CD is for WIRED networks; CSMA/CA is for WIRELESS (can't detect collision reliably due to hidden terminal problem).
- Distance Vector uses Bellman-Ford (shares whole table with neighbors); Link State uses Dijkstra (shares own links with everyone).
- A Switch operates at Layer 2 using MAC addresses; a Router operates at Layer 3 using IP addresses.
- Class A/B/C boundaries based on FIRST few bits of the address, not just the range — good to know binary pattern (Class A starts with 0, Class B with 10, Class C with 110).
