# NMap (Network Mapper)

**Nmap** (Network Mapper) is a free and open-source network scanner used to discover hosts and services on a computer network. It's one of the most popular network security tools.

Project Homepage: [Nmap](https://nmap.org/)
Documentation: [Nmap Reference Guide](https://nmap.org/book/man.html)

---

## Installation

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install nmap
```

**macOS:**
```bash
brew install nmap
```

**Verify Installation:**
```bash
nmap --version
```

---

## Basic Scanning

**Scan Single Host:**
```bash
# Basic scan
nmap example.com
nmap 192.168.1.1

# Scan specific ports
nmap -p 80,443 example.com

# Scan port range
nmap -p 1-1000 example.com

# Scan all ports
nmap -p- example.com
```

**Scan Multiple Hosts:**
```bash
# Scan multiple hosts
nmap 192.168.1.1 192.168.1.2

# Scan IP range
nmap 192.168.1.1-254

# Scan subnet
nmap 192.168.1.0/24

# Scan from file
nmap -iL targets.txt
```

---

## Scan Types

### TCP SYN Scan (Stealth Scan)
```bash
# Default scan type (requires root)
sudo nmap -sS 192.168.1.1
```
- Fast and stealthy
- Doesn't complete TCP handshake
- Most popular scan type

### TCP Connect Scan
```bash
# Full TCP connection
nmap -sT 192.168.1.1
```
- Completes TCP handshake
- Doesn't require root privileges
- More detectable

### UDP Scan
```bash
# Scan UDP ports
sudo nmap -sU 192.168.1.1

# Scan specific UDP ports
sudo nmap -sU -p 53,67,161 192.168.1.1
```
- Slower than TCP scans
- Important for DNS, DHCP, SNMP

### Other Scan Types
```bash
# TCP ACK scan (firewall detection)
sudo nmap -sA 192.168.1.1

# TCP Window scan
sudo nmap -sW 192.168.1.1

# TCP Null scan
sudo nmap -sN 192.168.1.1

# TCP FIN scan
sudo nmap -sF 192.168.1.1

# TCP Xmas scan
sudo nmap -sX 192.168.1.1
```

---

## Host Discovery

**Ping Scan:**
```bash
# Ping scan only (no port scan)
nmap -sn 192.168.1.0/24

# Disable ping (assume all hosts are up)
nmap -Pn 192.168.1.1

# TCP SYN ping
nmap -PS22,80,443 192.168.1.0/24

# TCP ACK ping
nmap -PA80,443 192.168.1.0/24

# UDP ping
sudo nmap -PU 192.168.1.0/24

# ICMP echo ping
nmap -PE 192.168.1.0/24
```

---

## Service and Version Detection

**Version Detection:**
```bash
# Detect service versions
nmap -sV 192.168.1.1

# Aggressive version detection
nmap -sV --version-intensity 9 192.168.1.1

# Light version detection (faster)
nmap -sV --version-intensity 2 192.168.1.1

# Version detection with OS detection
nmap -sV -O 192.168.1.1
```

---

## OS Detection

```bash
# OS detection
sudo nmap -O 192.168.1.1

# Aggressive OS detection
sudo nmap -O --osscan-guess 192.168.1.1

# Limit OS detection to promising targets
sudo nmap -O --osscan-limit 192.168.1.0/24
```

---

## Nmap Scripting Engine (NSE)

**Script Categories:**
- `auth` - Authentication scripts
- `broadcast` - Broadcast discovery
- `brute` - Brute force attacks
- `default` - Default scripts
- `discovery` - Network discovery
- `dos` - Denial of service
- `exploit` - Exploitation scripts
- `external` - External resources
- `fuzzer` - Fuzzing
- `intrusive` - Intrusive scripts
- `malware` - Malware detection
- `safe` - Safe scripts
- `version` - Version detection
- `vuln` - Vulnerability detection

**Run Scripts:**
```bash
# Run default scripts
nmap -sC 192.168.1.1

# Run specific script
nmap --script=http-title 192.168.1.1

# Run script category
nmap --script=vuln 192.168.1.1

# Run multiple scripts
nmap --script=http-title,http-headers 192.168.1.1

# Run all scripts except intrusive
nmap --script "not intrusive" 192.168.1.1

# Get script help
nmap --script-help http-title
```

**Popular Scripts:**
```bash
# HTTP vulnerability scan
nmap --script=http-vuln-* 192.168.1.1

# SSL/TLS information
nmap --script=ssl-cert,ssl-enum-ciphers -p 443 192.168.1.1

# SMB vulnerability scan
nmap --script=smb-vuln-* 192.168.1.1

# SSH information
nmap --script=ssh-* -p 22 192.168.1.1

# DNS brute force
nmap --script=dns-brute example.com

# Heartbleed detection
nmap --script=ssl-heartbleed -p 443 192.168.1.1

# Shellshock detection
nmap --script=http-shellshock -p 80 192.168.1.1
```

---

## Timing and Performance

**Timing Templates:**
```bash
# Paranoid (0) - Very slow, IDS evasion
nmap -T0 192.168.1.1

# Sneaky (1) - Slow, IDS evasion
nmap -T1 192.168.1.1

# Polite (2) - Slower, less bandwidth
nmap -T2 192.168.1.1

# Normal (3) - Default
nmap -T3 192.168.1.1

# Aggressive (4) - Fast, assumes good network
nmap -T4 192.168.1.1

# Insane (5) - Very fast, may miss results
nmap -T5 192.168.1.1
```

**Custom Timing:**
```bash
# Parallel scans
nmap --min-parallelism 100 192.168.1.0/24

# Host timeout
nmap --host-timeout 5m 192.168.1.0/24

# Scan delay
nmap --scan-delay 1s 192.168.1.1
```

---

## Output Formats

**Save Results:**
```bash
# Normal output
nmap -oN scan.txt 192.168.1.1

# XML output
nmap -oX scan.xml 192.168.1.1

# Grepable output
nmap -oG scan.gnmap 192.168.1.1

# All formats
nmap -oA scan 192.168.1.1

# Script kiddie output
nmap -oS scan.skid 192.168.1.1
```

---

## Advanced Scanning Techniques

**Firewall/IDS Evasion:**
```bash
# Fragment packets
nmap -f 192.168.1.1

# Specify MTU
nmap --mtu 24 192.168.1.1

# Decoy scan
nmap -D RND:10 192.168.1.1
nmap -D decoy1,decoy2,ME 192.168.1.1

# Spoof source IP (raw packets only)
nmap -S 192.168.1.100 192.168.1.1

# Randomize host order
nmap --randomize-hosts 192.168.1.0/24

# Spoof MAC address
nmap --spoof-mac Apple 192.168.1.1
```

**Custom Packets:**
```bash
# Custom TCP flags
nmap --scanflags SYNFIN 192.168.1.1

# Custom source port
nmap --source-port 53 192.168.1.1

# IPv6 scan
nmap -6 fe80::1
```

---

## Practical Examples

### Network Discovery
```bash
# Find live hosts on network
nmap -sn 192.168.1.0/24

# Fast scan of common ports
nmap -F 192.168.1.0/24

# Comprehensive network scan
nmap -sS -sV -O -p- -T4 192.168.1.0/24 -oA network_scan
```

### Web Server Scan
```bash
# HTTP/HTTPS scan with scripts
nmap -p 80,443 --script=http-title,http-headers,ssl-cert example.com

# Comprehensive web scan
nmap -p 80,443 --script=http-* example.com
```

### Vulnerability Assessment
```bash
# Run all vulnerability scripts
nmap --script=vuln 192.168.1.1

# Specific vulnerability scan
nmap --script=smb-vuln-ms17-010 192.168.1.1
```

### Database Server Scan
```bash
# MySQL scan
nmap -p 3306 --script=mysql-* 192.168.1.1

# PostgreSQL scan
nmap -p 5432 --script=pgsql-* 192.168.1.1

# MongoDB scan
nmap -p 27017 --script=mongodb-* 192.168.1.1
```

---

## Interpreting Results

**Port States:**
- `open` - Application accepting connections
- `closed` - Port accessible but no application
- `filtered` - Firewall/filter blocking probe
- `unfiltered` - Accessible but open/closed unknown
- `open|filtered` - Can't determine if open or filtered
- `closed|filtered` - Can't determine if closed or filtered

---

## Related Topics

- [[networking/tcp-vs-udp|TCP vs UDP]] - Transport protocols
- [[networking/tls|TLS]] - Secure communications
- [[linux/iptables|iptables]] - Linux firewall
- [[tools/openssh|OpenSSH]] - Secure shell

---

## Best Practices

**Legal and Ethical:**
- ⚠️ Only scan networks you own or have permission to scan
- Unauthorized scanning may be illegal
- Use caution with vulnerability scripts
- Document authorization for penetration testing

**Operational:**
- Start with less intrusive scans
- Use appropriate timing (avoid -T5 on production)
- Save scan results for documentation
- Run scans during maintenance windows
- Be aware of IDS/IPS alerts
- Understand network topology before scanning

**Performance:**
- Use `-F` for fast scans of common ports
- Use `-T4` for faster scans on reliable networks
- Limit scope to necessary targets
- Use `--top-ports` for most common ports
- Consider `--min-rate` for consistent scanning speed

---

## Common Nmap Commands Cheat Sheet

```bash
# Quick scan
nmap -F 192.168.1.1

# Comprehensive scan
nmap -A 192.168.1.1

# Aggressive scan with timing
nmap -A -T4 192.168.1.1

# Scan specific ports
nmap -p 22,80,443 192.168.1.1

# Version and OS detection
sudo nmap -sV -O 192.168.1.1

# Network discovery
nmap -sn 192.168.1.0/24

# Vulnerability scan
nmap --script=vuln 192.168.1.1

# Save all output formats
nmap -oA scan_results 192.168.1.1
```

---

## Additional Resources

- [Nmap Network Scanning Book](https://nmap.org/book/)
- [NSE Script Documentation](https://nmap.org/nsedoc/)
- [Nmap Cheat Sheet](https://www.stationx.net/nmap-cheat-sheet/)