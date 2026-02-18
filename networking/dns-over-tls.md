# DNS over TLS (DoT)

**DNS over TLS (DoT)** is a security protocol that encrypts DNS queries using the Transport Layer Security (TLS) protocol. It prevents eavesdropping and manipulation of DNS data.

RFC: [RFC 7858 - DNS over TLS](https://tools.ietf.org/html/rfc7858)

---

## What is DNS over TLS?

Traditional DNS queries are sent in plain text, making them vulnerable to:
- **Eavesdropping**: ISPs and others can see what websites you visit
- **Manipulation**: DNS responses can be altered (DNS spoofing)
- **Censorship**: DNS queries can be blocked

**DoT solves these issues by:**
- Encrypting DNS queries and responses
- Authenticating the DNS server
- Ensuring data integrity

---

## How It Works

1. Client establishes TLS connection to DNS server on **port 853**
2. TLS handshake authenticates the server
3. DNS queries are sent over encrypted TLS connection
4. DNS responses are received over same encrypted connection

**Key Difference from Regular DNS:**
- Regular DNS: Port 53 (unencrypted)
- DNS over TLS: Port 853 (encrypted)

---

## Benefits

✅ **Privacy**: DNS queries hidden from ISPs and network observers
✅ **Security**: Protection against DNS spoofing and MITM attacks
✅ **Integrity**: Ensures DNS responses haven't been tampered with
✅ **Authentication**: Verifies identity of DNS server
✅ **Compliance**: Helps meet privacy regulations

---

## DoT vs DoH

| Feature | DNS over TLS (DoT) | DNS over HTTPS (DoH) |
|---------|-------------------|---------------------|
| Port | 853 (dedicated) | 443 (HTTPS port) |
| Protocol | TLS | HTTPS |
| Detection | Easy to detect/block | Harder to detect (looks like HTTPS) |
| Network Policy | Easier to enforce | Harder to enforce |
| Standard | RFC 7858 | RFC 8484 |
| Performance | Slightly faster | Slightly more overhead |

**When to use DoT:**
- Enterprise networks with DNS policies
- When you want dedicated DNS traffic
- Easier network monitoring and debugging

**When to use DoH:**
- Bypass DNS blocking/censorship
- When DoT port 853 is blocked
- Browser-based applications

---

## Public DoT Servers

### Cloudflare
```
1.1.1.1 (one.one.one.one)
1.0.0.1
2606:4700:4700::1111
2606:4700:4700::1001
```

### Google
```
8.8.8.8 (dns.google)
8.8.4.4
2001:4860:4860::8888
2001:4860:4860::8844
```

### Quad9
```
9.9.9.9 (dns.quad9.net)
149.112.112.112
2620:fe::fe
2620:fe::9
```

### AdGuard DNS
```
94.140.14.14 (dns.adguard.com)
94.140.15.15
2a10:50c0::ad1:ff
2a10:50c0::ad2:ff
```

---

## Configuration

### Linux (systemd-resolved)

Edit `/etc/systemd/resolved.conf`:
```ini
[Resolve]
DNS=1.1.1.1#one.one.one.one
DNSOverTLS=yes
```

Restart service:
```bash
sudo systemctl restart systemd-resolved
```

Verify:
```bash
resolvectl status
```

### Using Stubby

**Install Stubby:**
```bash
# Ubuntu/Debian
sudo apt install stubby

# macOS
brew install stubby
```

**Configure** (`/etc/stubby/stubby.yml`):
```yaml
resolution_type: GETDNS_RESOLUTION_STUB
dns_transport_list:
  - GETDNS_TRANSPORT_TLS
tls_authentication: GETDNS_AUTHENTICATION_REQUIRED
tls_query_padding_blocksize: 128
idle_timeout: 10000
round_robin_upstreams: 1

upstream_recursive_servers:
  - address_data: 1.1.1.1
    tls_auth_name: "one.one.one.one"
  - address_data: 1.0.0.1
    tls_auth_name: "one.one.one.one"
```

**Start Stubby:**
```bash
sudo systemctl start stubby
sudo systemctl enable stubby
```

**Configure system to use Stubby:**
Edit `/etc/resolv.conf`:
```
nameserver 127.0.0.1
```

### Android 9+

Built-in support for DNS over TLS:

1. Go to **Settings** → **Network & Internet** → **Private DNS**
2. Select **Private DNS provider hostname**
3. Enter: `one.one.one.one` or `dns.google`

### iOS 14+

Install DNS profile (e.g., from Cloudflare, AdGuard)

### Windows

Use third-party tools:
- Stubby
- SimpleDNSCrypt
- YogaDNS

### macOS

Using Stubby (via Homebrew):
```bash
brew install stubby
brew services start stubby
```

Configure DNS to 127.0.0.1 in Network Settings.

---

## Using Unbound

**Install Unbound:**
```bash
sudo apt install unbound
```

**Configure** (`/etc/unbound/unbound.conf.d/dot.conf`):
```
server:
    interface: 127.0.0.1
    port: 53
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    access-control: 127.0.0.0/8 allow

forward-zone:
    name: "."
    forward-tls-upstream: yes
    forward-addr: 1.1.1.1@853#one.one.one.one
    forward-addr: 1.0.0.1@853#one.one.one.one
```

**Start Unbound:**
```bash
sudo systemctl restart unbound
```

---

## Testing DNS over TLS

**Using kdig:**
```bash
# Install knot-dnsutils
sudo apt install knot-dnsutils

# Test DoT query
kdig -d @1.1.1.1 +tls example.com

# Verify TLS
kdig @1.1.1.1 +tls-ca +tls-host=one.one.one.one example.com
```

**Using openssl:**
```bash
# Connect to DoT server
openssl s_client -connect 1.1.1.1:853

# Test with specific hostname
openssl s_client -connect 1.1.1.1:853 -servername one.one.one.one
```

**Verify DoT is working:**
```bash
# Check if queries are encrypted
sudo tcpdump -i any port 853

# Should see encrypted traffic, not plain DNS
```

**Online Tests:**
- https://1.1.1.1/help
- https://www.dnsleaktest.com/

---

## Docker Container with DoT

**Using Stubby in Docker:**
```dockerfile
FROM alpine:latest

RUN apk add --no-cache stubby

COPY stubby.yml /etc/stubby/stubby.yml

EXPOSE 53/udp 53/tcp

CMD ["stubby", "-C", "/etc/stubby/stubby.yml"]
```

**Docker Compose:**
```yaml
version: '3'
services:
  stubby:
    image: stubby-dot
    ports:
      - "53:53/udp"
      - "53:53/tcp"
    restart: unless-stopped
```

---

## Router Configuration

Many routers support DoT:
- UniFi (via command line)
- pfSense (via packages)
- OPNsense (built-in)
- OpenWrt (via packages)

**Example: pfSense with Unbound**
1. Install Unbound DNS resolver
2. Configure forwarding to DoT provider
3. Enable DNS over TLS in Unbound settings

---

## Performance Considerations

**Pros:**
- Minimal overhead compared to DoH
- Dedicated port (easier monitoring)
- Slightly faster than DoH

**Cons:**
- Additional TLS handshake latency (first query)
- Can be blocked on port 853
- May not work on restricted networks

**Optimization:**
- Use persistent TLS connections
- Enable connection reuse
- Use nearby DoT servers
- Consider caching resolver (Unbound, dnsmasq)

---

## Privacy Considerations

**What DoT Protects:**
- DNS query content from ISP/observers
- DNS query/response integrity
- Against DNS spoofing

**What DoT Doesn't Protect:**
- Your IP address (visible to DNS provider)
- SNI in TLS handshakes (shows domain)
- Destination IP addresses in network traffic

**For More Privacy:**
- Use DoT with VPN
- Use privacy-focused DNS providers (Quad9, AdGuard)
- Consider DNS over Tor for maximum privacy
- Enable ECH (Encrypted Client Hello) when available

---

## Troubleshooting

**Common Issues:**

**Port 853 blocked:**
```bash
# Test connectivity
telnet 1.1.1.1 853
nc -zv 1.1.1.1 853

# Use DoH instead if port is blocked
```

**DNS resolution slow:**
```bash
# Check latency
kdig @1.1.1.1 +tls example.com | grep "Query time"

# Try different provider
kdig @8.8.8.8 +tls example.com
```

**Certificate verification fails:**
```bash
# Check certificate
openssl s_client -connect 1.1.1.1:853 -servername one.one.one.one

# Update ca-certificates
sudo apt update && sudo apt install ca-certificates
```

**Stubby not working:**
```bash
# Check status
sudo systemctl status stubby

# View logs
sudo journalctl -u stubby -f

# Test configuration
stubby -C /etc/stubby/stubby.yml -l
```

---

## Related Topics

- [[networking/dns-over-https|DNS over HTTPS]] - Alternative DNS encryption
- [[networking/dns-encryption|DNS Encryption]] - Overview of DNS security
- [[networking/tls|TLS]] - Transport Layer Security protocol
- [[networking/vpn|VPN]] - Virtual Private Networks
- [[networking/dns-record-types|DNS Record Types]] - DNS basics

---

## Best Practices

✅ Use reputable DoT providers
✅ Verify TLS certificates
✅ Test for DNS leaks regularly
✅ Use DoT at router level for network-wide protection
✅ Keep resolver software updated
✅ Monitor DNS resolution performance
✅ Have fallback DNS configured
✅ Consider geographic location of DNS servers
✅ Review DNS provider privacy policy
✅ Combine with VPN for maximum privacy

---

## Security Notes

🔒 DoT encrypts queries but not immune to all attacks
🔒 DNS provider can still see your queries
🔒 Choose privacy-respecting providers
🔒 Beware of DNS provider logging policies
🔒 Corporate networks may block port 853
🔒 DoT doesn't hide IP addresses of accessed sites
🔒 Consider using multiple DNS providers for redundancy