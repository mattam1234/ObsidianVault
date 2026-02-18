# TCP vs UDP

**TCP** (Transmission Control Protocol) and **UDP** (User Datagram Protocol) are the two most common transport layer protocols used in networking.

---

## Quick Comparison

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Connection-oriented | Connectionless |
| Reliability | Reliable (guaranteed delivery) | Unreliable (best effort) |
| Ordering | Ordered delivery | No ordering guarantee |
| Speed | Slower (overhead) | Faster (minimal overhead) |
| Error Checking | Extensive | Basic checksum only |
| Flow Control | Yes | No |
| Congestion Control | Yes | No |
| Header Size | 20-60 bytes | 8 bytes |
| Use Cases | Web, Email, File transfer | Streaming, Gaming, DNS, VoIP |

---

## TCP (Transmission Control Protocol)

**Characteristics:**
- **Connection-oriented**: Establishes connection before data transfer
- **Reliable**: Guarantees delivery and ordering
- **Error checking**: Detects and retransmits lost packets
- **Flow control**: Prevents overwhelming the receiver
- **Congestion control**: Adapts to network conditions

### TCP Three-Way Handshake

**Connection Establishment:**
```
Client → Server: SYN
Server → Client: SYN-ACK
Client → Server: ACK
[Connection Established]
```

**Connection Termination:**
```
Client → Server: FIN
Server → Client: ACK
Server → Client: FIN
Client → Server: ACK
[Connection Closed]
```

### TCP Features

**Reliability:**
- Acknowledgments for received data
- Retransmission of lost packets
- Sequence numbers for ordering
- Checksums for error detection

**Flow Control:**
- Sliding window protocol
- Prevents buffer overflow
- Receiver controls sending rate

**Congestion Control:**
- Slow start
- Congestion avoidance
- Fast retransmit
- Fast recovery

### TCP Header

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Data |       |C|E|U|A|P|R|S|F|                               |
| Offset| Rsrvd |W|C|R|C|S|S|Y|I|            Window             |
|       |       |R|E|G|K|H|T|N|N|                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

**Header Size:** 20-60 bytes (20 bytes minimum, up to 40 bytes for options)

### Common TCP Ports

```
20/21  - FTP (File Transfer Protocol)
22     - SSH (Secure Shell)
23     - Telnet
25     - SMTP (Email)
80     - HTTP (Web)
443    - HTTPS (Secure Web)
3306   - MySQL
5432   - PostgreSQL
3389   - RDP (Remote Desktop)
```

---

## UDP (User Datagram Protocol)

**Characteristics:**
- **Connectionless**: No connection establishment
- **Unreliable**: No delivery guarantee
- **No ordering**: Packets may arrive out of order
- **Fast**: Minimal overhead
- **Simple**: No complex state management

### UDP Features

**Simplicity:**
- Fire and forget
- No connection setup/teardown
- No acknowledgments
- No retransmissions

**Low Overhead:**
- Small header (8 bytes)
- No flow control
- No congestion control
- Minimal processing

**Real-time Capable:**
- Low latency
- No buffering delays
- Suitable for time-sensitive data

### UDP Header

```
 0      7 8     15 16    23 24    31
+--------+--------+--------+--------+
|     Source      |   Destination   |
|      Port       |      Port       |
+--------+--------+--------+--------+
|                 |                 |
|     Length      |    Checksum     |
+--------+--------+--------+--------+
|                                   |
|          Data (payload)           |
|                                   |
+-----------------------------------+
```

**Header Size:** 8 bytes (fixed)

### Common UDP Ports

```
53     - DNS (Domain Name System)
67/68  - DHCP (Dynamic Host Configuration)
69     - TFTP (Trivial File Transfer)
123    - NTP (Network Time Protocol)
161    - SNMP (Simple Network Management)
514    - Syslog
1194   - OpenVPN
```

---

## When to Use TCP

✅ **Use TCP when you need:**
- Guaranteed delivery
- Ordered data
- Error correction
- File transfers
- Web browsing
- Email
- Database connections
- API calls

**TCP Use Cases:**
- **HTTP/HTTPS** - Web traffic
- **FTP/SFTP** - File transfers
- **SMTP/POP3/IMAP** - Email
- **SSH** - Remote access
- **Database connections** - MySQL, PostgreSQL
- **REST APIs** - Reliable communication

---

## When to Use UDP

✅ **Use UDP when you need:**
- Low latency over reliability
- Real-time data
- Broadcasting/multicasting
- Small, independent messages
- Can tolerate packet loss

**UDP Use Cases:**
- **DNS** - Quick lookups (small requests)
- **Streaming** - Video/audio (some loss acceptable)
- **Gaming** - Real-time updates (speed > accuracy)
- **VoIP** - Voice calls (low latency critical)
- **IoT sensors** - Frequent small updates
- **DHCP** - Network configuration
- **NTP** - Time synchronization

---

## Performance Considerations

**TCP Performance:**
- Higher latency due to handshakes
- Buffering can introduce delays
- Congestion control may throttle speed
- Better for bulk data transfer
- Protected from network congestion

**UDP Performance:**
- Lower latency (no handshakes)
- No retransmission delays
- Can overwhelm network if unchecked
- Better for real-time applications
- Requires application-level reliability if needed

**Optimization Notes:**

Each frame goes through several buffers:
- Application buffer
- Protocol buffer
- Software interface buffer
- Hardware interface buffer

When sending high-speed data, these buffers can fill up, causing blocking or data loss. Buffer sizes and polling strategies impact performance significantly.

**Performance Tips:**
- **TCP** is optimized for high-speed bulk transfers
- **UDP** is optimized for low latency
- Larger buffers with less frequent polling can improve throughput but increase latency
- Hardware offloading often available for TCP
- Generally expect better performance with TCP for large transfers

---

## Hybrid Protocols

Some protocols use both TCP and UDP:

**DNS:**
- UDP for quick queries (port 53)
- TCP for zone transfers or large responses

**HTTP/3 (QUIC):**
- Built on UDP
- Implements reliability at application layer
- Combines TCP benefits with UDP speed

**DTLS (Datagram TLS):**
- Secure version of UDP
- TLS-like security for UDP

---

## Testing TCP vs UDP

**Test TCP Connection:**
```bash
# Telnet to TCP port
telnet example.com 80

# Netcat TCP
nc -v example.com 80

# Nmap TCP scan
nmap -sT example.com
```

**Test UDP Connection:**
```bash
# Netcat UDP
nc -u example.com 53

# Nmap UDP scan
sudo nmap -sU example.com

# Test with specific tool (e.g., dig for DNS)
dig @8.8.8.8 example.com
```

**Packet Capture:**
```bash
# Capture TCP traffic
sudo tcpdump -i eth0 tcp port 80

# Capture UDP traffic
sudo tcpdump -i eth0 udp port 53

# Wireshark filters
tcp.port == 80
udp.port == 53
```

---

## Firewall Configuration

**iptables (TCP):**
```bash
# Allow TCP port 80
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Allow established TCP connections
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

**iptables (UDP):**
```bash
# Allow UDP port 53
sudo iptables -A INPUT -p udp --dport 53 -j ACCEPT

# Allow UDP with connection tracking
sudo iptables -A INPUT -p udp -m state --state ESTABLISHED -j ACCEPT
```

---

## Application Layer Protocols

**Reliability Implementation:**

Since UDP is unreliable, applications must implement reliability if needed:
- **QUIC** - Reliable transport over UDP (HTTP/3)
- **RTP** - Real-time protocol (with RTCP for control)
- **Custom protocols** - Application-specific reliability

**Congestion Control:**

⚠️ **Important:** Sending high-speed data over UDP without congestion control is usually a bad idea. TCP protects the network from congestion collapse.

**When using UDP for high-volume data:**
- Implement custom congestion control
- Rate limiting
- Monitor packet loss
- Adjust transmission rate

---

## Troubleshooting

**TCP Issues:**
```bash
# Check established connections
netstat -tn

# View TCP statistics
netstat -st

# Check for retransmissions
ss -ti

# Monitor TCP connections
watch -n 1 'ss -t'
```

**UDP Issues:**
```bash
# Check UDP sockets
netstat -un

# View UDP statistics
netstat -su

# Monitor UDP traffic
tcpdump -i any udp

# Check for drops
netstat -s | grep -i "udp\|datagram"
```

---

## Security Considerations

**TCP:**
- SYN flood attacks (DDoS)
- Connection hijacking
- Sequence number prediction
- Port scanning

**UDP:**
- UDP flood attacks
- Amplification attacks (DNS, NTP)
- Spoofing (no handshake)
- No inherent authentication

**Mitigations:**
- Firewalls
- Rate limiting
- Connection tracking
- DDoS protection
- Application-level authentication

---

## Related Topics

- [[networking/tls|TLS]] - Secure TCP connections
- [[linux/iptables|iptables]] - Linux firewall
- [[networking/dns-record-types|DNS]] - Uses both TCP and UDP
- [[networking/vpn|VPN]] - Can use TCP or UDP
- [[tools/nmap|Nmap]] - Network scanning

---

## Best Practices

**For TCP:**
✅ Use for reliable data transfer
✅ Implement timeouts for connections
✅ Handle connection errors gracefully
✅ Monitor connection states
✅ Use keepalive for long-lived connections

**For UDP:**
✅ Use for real-time, latency-sensitive applications
✅ Keep messages small (avoid fragmentation)
✅ Implement application-level reliability if needed
✅ Don't flood the network without congestion control
✅ Consider packet loss in application design
✅ Use checksums to detect corruption

---

## Summary

**Choose TCP when:**
- Reliability is critical
- Data must arrive in order
- You can tolerate higher latency
- Large data transfers

**Choose UDP when:**
- Speed is more important than reliability
- Real-time communication
- Small, independent messages
- Broadcasting/multicasting needed
- Can handle packet loss at application level
