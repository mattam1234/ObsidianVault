# VPN (Virtual Private Network)

**VPN** (Virtual Private Network) creates a secure, encrypted connection over a less secure network, typically the internet. It extends a private network across a public network.

---

## What is a VPN?

A VPN allows users to:
- Send and receive data as if directly connected to a private network
- Access region-restricted websites and services
- Shield browsing activity from public Wi-Fi networks
- Bypass geo-blocking and censorship
- Hide IP addresses and online activities

**Key Benefits:**
- **Privacy**: Hides your IP address and online activities
- **Security**: Encrypts data transmission
- **Remote Access**: Connect to office networks remotely
- **Geo-spoofing**: Appear to be in different locations

---

## VPN Types

### Remote Access VPN
- Connects individual users to a private network
- Common for remote workers
- Examples: OpenVPN, WireGuard, corporate VPNs

### Site-to-Site VPN
- Connects entire networks together
- Used for branch offices
- Creates secure tunnels between locations

### SSL/TLS VPN
- Web-based VPN access
- No client software required
- Accessed through browsers

### IPsec VPN
- Network layer VPN protocol
- Strong encryption and authentication
- Common in enterprise environments

---

## VPN Protocols

### OpenVPN
- Open-source and highly configurable
- Uses SSL/TLS for encryption
- Works on TCP/UDP
- **Pros**: Very secure, flexible, cross-platform
- **Cons**: Can be slower, complex setup

### WireGuard
- Modern, lightweight protocol
- Simpler codebase (easier to audit)
- Very fast performance
- **Pros**: Fast, secure, simple
- **Cons**: Relatively new, fewer features

### IPsec (Internet Protocol Security)
- Suite of protocols for securing IP communications
- Native OS support
- **Modes**: Transport mode, Tunnel mode
- **Pros**: Industry standard, native support
- **Cons**: Complex configuration, NAT traversal issues

### IKEv2/IPsec
- Combines IKE with IPsec
- Excellent for mobile devices
- Auto-reconnects after network changes
- **Pros**: Fast, stable, mobile-friendly
- **Cons**: Limited platform support

### L2TP/IPsec
- Layer 2 Tunneling Protocol with IPsec
- Built into most operating systems
- **Pros**: Easy to set up, widely supported
- **Cons**: Can be blocked, potentially slower

### PPTP (Point-to-Point Tunneling Protocol)
- Older protocol
- **Status**: Deprecated due to security vulnerabilities
- **Not recommended** for secure communications

### SSTP (Secure Socket Tunneling Protocol)
- Microsoft proprietary protocol
- Uses SSL/TLS
- Works well through firewalls
- **Pros**: Integrates with Windows, firewall-friendly
- **Cons**: Primarily Windows-only

---

## Popular VPN Solutions

### Open Source
- **OpenVPN**: Industry-standard VPN protocol and software
- **WireGuard**: Modern, fast VPN protocol
- **SoftEther VPN**: Multi-protocol VPN server
- **strongSwan**: IPsec-based VPN solution
- **Pritunl**: OpenVPN-based VPN server with web UI

### Commercial VPN Services
- NordVPN
- ExpressVPN
- ProtonVPN
- Mullvad
- Surfshark

### Enterprise Solutions
- Cisco AnyConnect
- Palo Alto GlobalProtect
- Fortinet FortiClient
- Pulse Secure
- Check Point VPN

---

## Setting Up VPN

### OpenVPN Quick Start

**Server Installation (Ubuntu):**
```bash
# Install OpenVPN
sudo apt update
sudo apt install openvpn easy-rsa

# Setup PKI
make-cadir ~/openvpn-ca
cd ~/openvpn-ca
./easyrsa init-pki
./easyrsa build-ca
./easyrsa gen-dh
./easyrsa build-server-full server nopass
```

**Client Connection:**
```bash
# Install client
sudo apt install openvpn

# Connect to VPN
sudo openvpn --config client.ovpn
```

### WireGuard Quick Start

**Installation:**
```bash
# Ubuntu/Debian
sudo apt install wireguard

# Generate keys
wg genkey | tee privatekey | wg pubkey > publickey
```

**Configuration (`/etc/wireguard/wg0.conf`):**
```ini
[Interface]
PrivateKey = <server-private-key>
Address = 10.0.0.1/24
ListenPort = 51820

[Peer]
PublicKey = <client-public-key>
AllowedIPs = 10.0.0.2/32
```

**Start VPN:**
```bash
sudo wg-quick up wg0
sudo wg-quick down wg0
```

---

## VPN Security Considerations

**Encryption:**
- Use strong encryption (AES-256)
- Avoid deprecated protocols (PPTP)
- Enable Perfect Forward Secrecy

**DNS Leaks:**
- Configure VPN to handle DNS
- Use VPN provider's DNS servers
- Test for DNS leaks

**Kill Switch:**
- Blocks internet if VPN disconnects
- Prevents IP/DNS leaks
- Essential for privacy

**Logging Policies:**
- Check VPN provider's logging policy
- Prefer no-log or minimal-log providers
- Consider jurisdiction

**Authentication:**
- Use strong authentication methods
- Enable two-factor authentication
- Regularly rotate credentials

---

## Testing VPN Connection

**Check IP Address:**
```bash
# Before VPN
curl ifconfig.me

# After VPN connection
curl ifconfig.me
```

**DNS Leak Test:**
```bash
# Check DNS servers
nslookup google.com

# Online testing
# Visit: https://dnsleaktest.com/
```

**Speed Test:**
```bash
# Install speedtest-cli
pip install speedtest-cli

# Run speed test
speedtest-cli
```

---

## Common VPN Issues

**Connection Problems:**
- Firewall blocking VPN ports
- Incorrect credentials
- Certificate issues
- NAT traversal problems

**Performance Issues:**
- Slow VPN server
- Distance to VPN server
- Encryption overhead
- Network congestion

**Troubleshooting:**
```bash
# Check VPN status
sudo systemctl status openvpn@server

# View VPN logs
sudo journalctl -u openvpn@server -f

# Test connectivity
ping 10.0.0.1
traceroute 10.0.0.1
```

---

## VPN vs Proxy

| Feature | VPN | Proxy |
|---------|-----|-------|
| Encryption | Yes | Usually No |
| System-wide | Yes | Per-application |
| Speed | Slower (encryption) | Faster |
| Security | High | Low-Medium |
| Privacy | High | Medium |
| Cost | Paid/Free | Mostly Free |

---

## Use Cases

**Personal Use:**
- Secure public Wi-Fi connections
- Privacy from ISP tracking
- Access geo-restricted content
- Bypass censorship

**Business Use:**
- Remote employee access
- Secure branch office connections
- Protect sensitive data transmission
- Comply with regulations

**Development/Testing:**
- Test geo-specific features
- Access development servers
- Secure connections to cloud resources

---

## Related Topics

- [[tools/openssh|OpenSSH]] - SSH tunneling alternative
- [[networking/tls|TLS]] - Encryption protocol used in VPNs
- [[networking/tcp-vs-udp|TCP vs UDP]] - Transport protocols
- [[docker/docker-networking|Docker Networking]] - Container networking

---

## Best Practices

- Use modern VPN protocols (WireGuard, OpenVPN)
- Enable kill switch functionality
- Choose VPN servers close to your location
- Regularly update VPN software
- Use strong authentication
- Monitor for DNS leaks
- Review VPN provider's privacy policy
- Enable Perfect Forward Secrecy
- Test VPN connection regularly
- Keep configurations secure and backed up

---

## Additional Resources

- [WireGuard Official Site](https://www.wireguard.com/)
- [OpenVPN Documentation](https://openvpn.net/community-resources/)
- [VPN Comparison Guide](https://www.privacytools.io/providers/vpn/)