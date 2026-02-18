# DNS over HTTPS (DoH)

**DNS over HTTPS (DoH)** is a protocol for performing DNS resolution over the HTTPS protocol. It encrypts DNS queries using HTTPS, making them indistinguishable from regular web traffic.

RFC: [RFC 8484 - DNS Queries over HTTPS](https://tools.ietf.org/html/rfc8484)

---

## What is DNS over HTTPS?

Traditional DNS queries are sent in plain text, exposing:
- Which websites you visit
- Your browsing patterns
- Potential for DNS manipulation

**DoH solves this by:**
- Encrypting DNS queries inside HTTPS
- Making DNS traffic look like normal web traffic
- Preventing DNS blocking and filtering
- Protecting against DNS spoofing

---

## How It Works

1. Client sends DNS query as HTTPS POST/GET request to DoH server
2. Query is sent to port **443** (standard HTTPS port)
3. Server responds with DNS answer in HTTPS response
4. All traffic is encrypted and authenticated via TLS

**URL Format:**
```
https://dns.example.com/dns-query
```

**Wire Format (POST):**
```http
POST /dns-query HTTP/2
Host: dns.example.com
Content-Type: application/dns-message
Content-Length: 33

<binary DNS query>
```

**JSON Format (GET):**
```
https://dns.google/resolve?name=example.com&type=A
```

---

## DoH vs DoT

| Feature | DNS over HTTPS (DoH) | DNS over TLS (DoT) |
|---------|---------------------|-------------------|
| Port | 443 (HTTPS) | 853 (dedicated) |
| Protocol | HTTPS | TLS |
| Visibility | Hidden in HTTPS traffic | Visible on port 853 |
| Blocking | Harder to block | Easier to block |
| Browser Support | Native in modern browsers | Requires system config |
| Enterprise Control | More difficult | Easier |
| Performance | Slight overhead | Slightly faster |

**When to use DoH:**
- Bypass DNS blocking/censorship
- Browser-based privacy
- When port 853 is blocked
- Public Wi-Fi networks

**When to use DoT:**
- Enterprise environments
- Network-wide DNS policies
- System-level DNS encryption
- Easier debugging

---

## Public DoH Servers

### Cloudflare
```
URL: https://cloudflare-dns.com/dns-query
URL: https://1.1.1.1/dns-query
IPs: 1.1.1.1, 1.0.0.1
```

### Google
```
URL: https://dns.google/dns-query
JSON: https://dns.google/resolve
IPs: 8.8.8.8, 8.8.4.4
```

### Quad9
```
URL: https://dns.quad9.net/dns-query
IPs: 9.9.9.9, 149.112.112.112
```

### AdGuard DNS
```
URL: https://dns.adguard.com/dns-query
IPs: 94.140.14.14, 94.140.15.15
```

### NextDNS
```
URL: https://dns.nextdns.io/
Custom: https://dns.nextdns.io/[config-id]
```

---

## Browser Configuration

### Firefox

**Enable DoH:**
1. Go to `about:preferences#general`
2. Scroll to **Network Settings**
3. Click **Settings**
4. Enable **Enable DNS over HTTPS**
5. Choose provider or use custom URL

**Via about:config:**
```
network.trr.mode = 2
network.trr.uri = https://cloudflare-dns.com/dns-query
```

**TRR Modes:**
- `0` - DoH disabled
- `1` - DoH enabled, fall back to regular DNS
- `2` - DoH preferred, fall back if needed
- `3` - DoH only (no fallback)
- `5` - DoH disabled (explicit)

### Chrome/Edge

**Enable DoH:**
1. Go to `chrome://settings/security`
2. Enable **Use secure DNS**
3. Choose provider or enter custom URL

**Via Command Line:**
```bash
# Start Chrome with DoH
chrome --enable-features="DnsOverHttps" \
       --dns-over-https-server-url="https://cloudflare-dns.com/dns-query"
```

### Safari

Safari uses system-level DNS settings. Configure DoH at macOS level.

---

## System Configuration

### Linux (systemd-resolved)

DoH support is limited. Use dnsproxy or cloudflared instead.

### Using cloudflared

**Install:**
```bash
# Download and install cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
```

**Configure:**
```bash
# Run as DNS proxy
cloudflared proxy-dns --port 5053 --upstream https://1.1.1.1/dns-query

# Configure system DNS to 127.0.0.1:5053
```

**System Service:**
```bash
# Install as service
sudo cloudflared service install

# Start service
sudo systemctl start cloudflared
sudo systemctl enable cloudflared
```

### Using dnsproxy

**Install:**
```bash
# Download from https://github.com/AdguardTeam/dnsproxy/releases
wget https://github.com/AdguardTeam/dnsproxy/releases/latest/download/dnsproxy-linux-amd64.tar.gz
tar -xzf dnsproxy-linux-amd64.tar.gz
```

**Run:**
```bash
./dnsproxy -u https://dns.adguard.com/dns-query -l 127.0.0.1 -p 5353
```

### Android 11+

Some DNS apps support DoH:
- Intra (by Jigsaw)
- Nebulo
- PersonalDNSFilter

### iOS/macOS

Install configuration profiles:
- Cloudflare's 1.1.1.1 app
- AdGuard DNS app
- NextDNS app

**macOS (via CLI):**
```bash
# Install dnscrypt-proxy
brew install dnscrypt-proxy

# Edit /usr/local/etc/dnscrypt-proxy.toml
server_names = ['cloudflare']

# Start service
sudo brew services start dnscrypt-proxy
```

### Windows

**Using Simple DNSCrypt:**
1. Download Simple DNSCrypt
2. Select DoH provider
3. Install Windows service

**Using cloudflared:**
```powershell
# Download cloudflared
# Run as service
cloudflared.exe proxy-dns --port 53 --upstream https://1.1.1.1/dns-query
```

---

## Testing DoH

**Using curl:**
```bash
# Query using GET (JSON API)
curl -H 'accept: application/dns-json' \
  'https://cloudflare-dns.com/dns-query?name=example.com&type=A'

# Query using POST (wire format)
curl -H 'content-type: application/dns-message' \
  --data-binary @dns-query.bin \
  'https://cloudflare-dns.com/dns-query'
```

**Using dog:**
```bash
# Install dog
cargo install dog

# DoH query
dog example.com --https @https://cloudflare-dns.com/dns-query
```

**Using kdig:**
```bash
# Install knot-dnsutils
sudo apt install knot-dnsutils

# DoH query
kdig -d @1.1.1.1 +https example.com
```

**Browser DevTools:**
1. Open browser DevTools (F12)
2. Go to Network tab
3. Look for requests to `dns-query` endpoint
4. Verify encrypted DNS queries

**Online Tests:**
- https://1.1.1.1/help
- https://www.dnsleaktest.com/
- https://browserleaks.com/dns

---

## Docker Configuration

**Cloudflared Container:**
```dockerfile
FROM cloudflare/cloudflared:latest

EXPOSE 53/udp
EXPOSE 53/tcp

CMD ["proxy-dns", "--port", "53", "--address", "0.0.0.0", \
     "--upstream", "https://1.1.1.1/dns-query"]
```

**Docker Compose:**
```yaml
version: '3'

services:
  cloudflared:
    image: cloudflare/cloudflared:latest
    command: proxy-dns --port 53 --address 0.0.0.0 --upstream https://1.1.1.1/dns-query
    ports:
      - "53:53/udp"
      - "53:53/tcp"
    restart: unless-stopped
```

**Using dnsdist:**
```dockerfile
FROM powerdns/dnsdist-18

COPY dnsdist.conf /etc/dnsdist/dnsdist.conf

EXPOSE 53/udp 53/tcp

CMD ["dnsdist", "--supervised"]
```

**dnsdist.conf:**
```lua
-- Listen on port 53
setLocal("0.0.0.0:53")

-- Add DoH upstream
newServer({address="1.1.1.1:443", tls="openssl", 
           subjectName="cloudflare-dns.com", dohPath="/dns-query"})
```

---

## Router/Network Configuration

### Pi-hole with DoH

**Install cloudflared on Pi-hole:**
```bash
# Install cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-armhf
sudo mv cloudflared-linux-armhf /usr/local/bin/cloudflared
sudo chmod +x /usr/local/bin/cloudflared

# Create user
sudo useradd -s /usr/sbin/nologin -r -M cloudflared

# Configure
sudo cloudflared proxy-dns --port 5053 --upstream https://1.1.1.1/dns-query

# Install as service
sudo cloudflared service install
```

**Configure Pi-hole:**
1. Go to Settings → DNS
2. Uncheck all upstream DNS servers
3. Add custom DNS: `127.0.0.1#5053`

### OpenWrt

Install and configure `https-dns-proxy` package.

### UniFi

Configure via command line using cloudflared or dnsproxy.

---

## Performance Considerations

**Advantages:**
- Uses existing port 443 (firewall-friendly)
- Connection reuse with HTTP/2
- Caching improves performance

**Disadvantages:**
- HTTP overhead compared to DoT
- Requires HTTPS connection setup
- Slightly higher latency

**Optimization Tips:**
- Use HTTP/2 or HTTP/3 (QUIC)
- Enable connection keepalive
- Choose geographically close servers
- Use local caching resolver
- Consider CDN-based DoH providers

---

## Privacy Considerations

**What DoH Protects:**
- DNS queries from ISP/network observers
- Against DNS manipulation
- Query/response integrity
- Hides which websites you query

**What DoH Doesn't Protect:**
- Your IP address to DNS provider
- SNI in TLS (shows domain name)
- Destination IP addresses
- Traffic analysis (timing, size)

**Enhanced Privacy:**
- Choose no-log DNS providers
- Use DoH with VPN
- Enable ECH/ESNI (encrypted SNI)
- Consider DNS over Tor
- Rotate between multiple providers

**Privacy-Focused Providers:**
- Quad9 (no logging, security filtering)
- AdGuard (blocks ads, no logging option)
- NextDNS (customizable, logging optional)
- Mullvad (VPN provider's DNS)

---

## Security

**Benefits:**
- Prevents DNS hijacking
- Protects against MITM attacks
- Verifies DNS server authenticity
- Ensures response integrity

**Concerns:**
- Centralization of DNS (few large providers)
- Bypass of enterprise DNS policies
- Harder for network administrators to monitor
- Trust shifted to DoH provider

**Best Practices:**
🔒 Use reputable DoH providers
🔒 Verify TLS certificates
🔒 Monitor for DNS leaks
🔒 Keep DoH client software updated
🔒 Use providers with clear privacy policies
🔒 Consider running your own DoH resolver
🔒 Audit DNS query logs (if available)

---

## Troubleshooting

**DoH not working in browser:**
```
1. Check provider URL is correct
2. Disable browser extensions (may interfere)
3. Clear browser cache
4. Check network.trr.mode setting (Firefox)
5. Verify no corporate DNS policies
```

**Slow DNS resolution:**
```bash
# Test DoH server response time
curl -w "%{time_total}\n" \
  'https://cloudflare-dns.com/dns-query?name=example.com&type=A'

# Try different provider
# Check for local caching
```

**DNS leaks:**
```
1. Test at https://dnsleaktest.com/
2. Verify DoH is actually being used
3. Check for DNS bypass (WebRTC, etc.)
4. Ensure no fallback to system DNS
```

**cloudflared issues:**
```bash
# Check status
sudo systemctl status cloudflared

# View logs
sudo journalctl -u cloudflared -f

# Test manually
cloudflared proxy-dns --port 5053 \
  --upstream https://1.1.1.1/dns-query
```

---

## Related Topics

- [[networking/dns-over-tls|DNS over TLS]] - Alternative DNS encryption
- [[networking/dns-encryption|DNS Encryption]] - DNS security overview
- [[networking/tls|TLS]] - Transport Layer Security
- [[networking/dns-record-types|DNS Record Types]] - DNS basics
- [[networking/vpn|VPN]] - Virtual Private Networks

---

## Additional Resources

- [DNS Privacy Project](https://dnsprivacy.org/)
- [Cloudflare 1.1.1.1](https://1.1.1.1/)
- [Google Public DNS](https://developers.google.com/speed/public-dns)
- [Quad9 DNS](https://quad9.net/)
- [NextDNS](https://nextdns.io/)