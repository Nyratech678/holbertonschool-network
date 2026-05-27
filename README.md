---

## 📋 Table of Contents

- <a>OSI Model</a>
- <a>Network Types</a>
  - <a>LAN</a>
  - <a>WAN</a>
  - <a>The Internet</a>
- <a>IP Addressing</a>
- <a>TCP &amp; UDP</a>
- <a>Ping &amp; ICMP</a>
- <a>Positional Parameters</a>
- <a>Project Requirements</a>
- <a>Useful Commands</a>

---

## 🏗️ OSI Model

### What is it?
The **Open Systems Interconnection (OSI)** model is a **conceptual framework** that standardizes network communication into 7 abstraction layers. Each layer has a specific function and communicates with the layer directly above and below it.

&gt; **Key rule:** Layer *N* provides a service to Layer *N+1* and uses the service of Layer *N-1*.

### How many layers?
**7 layers.**

### How is it organized?

| Layer | Name | PDU | Function | Examples |
|:---:|:---|:---|:---|:---|
| **7** | Application | Data | User-facing protocols and interfaces | HTTP, HTTPS, FTP, SMTP, SSH, DNS |
| **6** | Presentation | Data | Translation, encryption, compression | TLS/SSL, MIME, ASCII, JPEG |
| **5** | Session | Data | Session management, dialog control | NetBIOS, PPTP, RTP, SOCKS |
| **4** | Transport | Segment | End-to-end delivery, reliability, ports | **TCP**, **UDP**, SCTP, QUIC |
| **3** | Network | Packet/Datagram | Logical addressing, routing, path selection | **IP** (v4/v6), **ICMP**, IPsec, OSPF |
| **2** | Data Link | Frame | Node-to-node delivery, MAC addressing | Ethernet, Wi-Fi (802.11), **ARP**, PPP |
| **1** | Physical | Bit | Raw bit transmission over physical medium | RJ45, USB, Bluetooth, DSL, fiber |

### Mnemonic (bottom to top)
&gt; **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way

---

## 🌐 Network Types

### LAN — Local Area Network

| Attribute | Detail |
|:---|:---|
| **Typical usage** | Connecting devices in a limited area (home, office, school, data center) |
| **Typical geographical size** | Small — from a single room to a building or campus (up to ~1-2 km) |
| **Ownership** | Usually privately owned |
| **Speed** | High bandwidth, low latency |
| **Technologies** | Ethernet (wired), Wi-Fi (wireless) |

### WAN — Wide Area Network

| Attribute | Detail |
|:---|:---|
| **Typical usage** | Connecting multiple LANs across cities, countries, or continents |
| **Typical geographical size** | Large — from a city to global scale |
| **Ownership** | Often uses leased lines or ISP infrastructure |
| **Speed** | Lower than LAN, higher latency |
| **Example** | The Internet itself is the largest WAN |

### The Internet

The **Internet** is a global system of interconnected computer networks that uses standardized communication protocols (TCP/IP) to link devices worldwide. It is essentially a **network of networks** — millions of private, public, academic, business, and government LANs and WANs interconnected.

---

## 🎯 IP Addressing

### What is an IP address?
An **Internet Protocol (IP) address** is a unique numerical identifier assigned to each device connected to a network that uses the IP protocol. It enables devices to locate and communicate with each other.

### What are the 2 types of IP address?

| Type | Description | Example |
|:---|:---|:---|
| **Public IP** | Assigned by your ISP; visible on the Internet; unique globally | `203.0.113.45` |
| **Private IP** | Used within a local network (LAN); not routable on the Internet | `192.168.1.10`, `10.0.0.5` |

&gt; **Private IP ranges (IPv4):** `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`

### What is localhost?
**localhost** is a hostname that refers to the **current device** itself. It always resolves to the loopback IP address:
- **IPv4:** `127.0.0.1`
- **IPv6:** `::1`

&gt; Traffic to localhost never leaves the device. It is used for testing and local development.

### What is a subnet?
A **subnet** (subnetwork) is a logical subdivision of an IP network. It splits a larger network into smaller, more manageable segments. Subnetting is controlled by the **subnet mask** (e.g., `255.255.255.0` or `/24`), which determines which portion of the IP address represents the network and which represents the host.

### Why was IPv6 created?
**IPv4 exhaustion.** IPv4 uses 32-bit addresses, providing approximately **4.3 billion** unique addresses. With the explosion of Internet-connected devices, this pool was depleted.

**IPv6** uses **128-bit addresses**, providing **340 undecillion** addresses (`3.4 × 10³⁸`). It also introduces:
- Simplified header format
- Built-in security (IPsec)
- Auto-configuration
- Better multicast and anycast support

| | IPv4 | IPv6 |
|:---|:---|:---|
| **Address size** | 32 bits | 128 bits |
| **Address format** | Dotted decimal (e.g., `192.168.1.1`) | Hex colon (e.g., `2001:0db8::1`) |
| **Total addresses** | ~4.3 billion | ~340 undecillion |
| **Header** | Variable length, checksum required | Fixed length, no checksum |

---

## 🔄 TCP &amp; UDP

### What are the 2 mainly used data transfer protocols for IP?
1. **TCP** — Transmission Control Protocol
2. **UDP** — User Datagram Protocol

Both operate at **Layer 4 (Transport)** of the OSI model.

### What is the main difference between TCP and UDP?

| Feature | TCP | UDP |
|:---|:---|:---|
| **Connection** | Connection-oriented (handshake: SYN → SYN-ACK → ACK) | Connectionless (no handshake) |
| **Reliability** | Reliable — acknowledgments, retransmissions, sequencing | Unreliable — "fire and forget" |
| **Order** | Guarantees in-order delivery | No ordering guarantee |
| **Speed** | Slower (overhead for reliability) | Faster (minimal overhead) |
| **Use case** | Web browsing, email, file transfer | Streaming, VoIP, gaming, DNS |

### What is a port?
A **port** is a 16-bit number (0–65535) that identifies a specific process or service on a host. It allows multiple applications to use the network simultaneously on the same IP address.

### Memorize these port numbers

| Protocol | Port | Purpose |
|:---|:---:|:---|
| **SSH** | **22** | Secure remote login and command execution |
| **HTTP** | **80** | Unencrypted web traffic |
| **HTTPS** | **443** | Encrypted web traffic (TLS/SSL) |

&gt; **Well-known ports:** 0–1023 (require root/admin privileges)
&gt; **Registered ports:** 1024–49151
&gt; **Dynamic/Private ports:** 49152–65535

### What tool/protocol is often used to check if a device is connected to a network?
**Ping** using the **ICMP** protocol.

---

## 📡 Ping &amp; ICMP

### What is ping?
`ping` is a command-line utility that tests the reachability of a host on an IP network by sending **ICMP Echo Request** packets and waiting for **ICMP Echo Reply** packets.

### How it works

```
┌─────────────┐                    ┌─────────────┐
│   SOURCE    │ ──ICMP Request──▶  │   TARGET    │
│  (your PC)  │                    │  (remote)   │
│  ⏱ START    │ ◀──ICMP Reply────  │             │
│  CALC RTT   │                    │             │
└─────────────┘                    └─────────────┘
```

- **RTT (Round-Trip Time)** is calculated **only by the source** machine.
- **TTL (Time To Live)** in the reply indicates how many routers were traversed.

### ICMP Types used by ping

| Type | Code | Name | Direction |
|:---:|:---:|:---|:---|
| 8 | 0 | Echo Request | Source → Target |
| 0 | 0 | Echo Reply | Target → Source |
| 11 | 0 | Time Exceeded | Router (TTL expired) |

### Key parameters

| Parameter | Windows Default | Linux Default |
|:---|:---|:---|
| Payload size | 32 bytes | 56 bytes |
| Count | 4 packets | Infinite (Ctrl+C to stop) |

### Common commands

```bash
# Basic ping
ping google.com

# Linux: 4 packets only
ping -c 4 google.com

# Windows: continuous ping
ping -t google.com

# Custom packet size
ping -s 1500 google.com    # Linux
ping -l 1500 google.com    # Windows
```

---

## 🧮 Bash Positional Parameters

When you pass arguments to a Bash script or function, they are stored in **positional parameters**.

### Reference table

| Parameter | Content | Type |
|:---|:---|:---|
| `$0` | Name of the script | Special |
| `$1` | 1st argument | Positional |
| `$2` | 2nd argument | Positional |
| `$3` | 3rd argument | Positional |
| `...` | ... | ... |
| `$9` | 9th argument | Positional |
| `${10}` | 10th argument | Positional |
| `$#` | Total number of arguments | Special |
| `$@` | All arguments as a **list** (preserves quotes) | Special |
| `$*` | All arguments as a **single string** | Special |

### ⚠️ Critical rules

1. **Beyond `$9`, use braces:** `${10}`, `${11}`, `${123}`
   - `$10` is interpreted as `${1}0` (first arg + character "0")

2. **Always use `"$@"` to relay arguments** — it preserves spaces inside quoted arguments.

3. **`shift` moves everything left** — `$2` becomes `$1`, `$3` becomes `$2`, etc.

### Example

```bash
#!/usr/bin/env bash
# This script demonstrates positional parameters

echo "Script name: $0"
echo "Total args:  $#"
echo "First arg:   $1"
echo "All args:    $@"

for arg in "$@"; do
    echo "-> $arg"
done
```

---

## ⚙️ Project Requirements

### General
- All Bash scripts run on **Ubuntu 22.04 LTS**
- Allowed editors: `vi`, `vim`, `emacs`
- All files must **end with a new line**
- A `README.md` at the root is **mandatory**
- All Bash scripts must be **executable** (`chmod u+x script.sh`)
- All scripts must pass **ShellCheck** without errors

### Script Header Template

Every Bash script **must** follow this exact format:

```bash
#!/usr/bin/env bash
# This script does [clear description of what the script does]

# Your code here...
```

| Line | Content | Mandatory? |
|:---:|:---|:---:|
| 1 | `#!/usr/bin/env bash` | ✅ Yes |
| 2 | Comment explaining the script | ✅ Yes |

---

## 🛠️ Useful Commands

### `netstat`
Displays network connections, routing tables, interface statistics, masquerade connections, and multicast memberships.

```bash
# Show all listening ports with process names
sudo netstat -tlnp

# Show all connections
netstat -a

# Show routing table
netstat -r
```

### `ping`
Tests network connectivity to a remote host.

```bash
# Standard ping
ping 8.8.8.8

# Ping with count limit (Linux)
ping -c 5 google.com

# Ping with timestamp (Linux)
ping -D google.com
```

### Quick checks

```bash
# Check your IP address
ip addr show

# Check your public IP
curl ifconfig.me

# Check if a port is open
nc -zv hostname 22
```

---
