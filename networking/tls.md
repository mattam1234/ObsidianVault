# TLS (Transport Layer Security)

**TLS** (Transport Layer Security) is a cryptographic protocol designed to provide secure communication over a computer network. It is the successor to SSL (Secure Sockets Layer).

---

## What is TLS?

TLS provides:
- **Privacy**: Data is encrypted between communicating parties
- **Authentication**: Verifies the identity of communicating parties
- **Integrity**: Detects message tampering or forgery

TLS is used in:
- HTTPS (HTTP over TLS)
- Email (SMTP, POP3, IMAP)
- VPNs
- Instant messaging
- VoIP

---

## TLS Versions

| Version | Released | Status |
|---------|----------|--------|
| SSL 2.0 | 1995 | Deprecated (insecure) |
| SSL 3.0 | 1996 | Deprecated (insecure) |
| TLS 1.0 | 1999 | Deprecated |
| TLS 1.1 | 2006 | Deprecated |
| TLS 1.2 | 2008 | Widely used |
| TLS 1.3 | 2018 | Current standard |

**Recommendation**: Use TLS 1.2 or TLS 1.3 only

---

## TLS Handshake Process

The TLS handshake establishes a secure connection:

1. **Client Hello**
   - Client sends supported TLS versions and cipher suites
   - Includes random data for key generation

2. **Server Hello**
   - Server selects TLS version and cipher suite
   - Sends server certificate
   - Includes random data for key generation

3. **Certificate Verification**
   - Client verifies server certificate against trusted CAs
   - Checks certificate validity and hostname

4. **Key Exchange**
   - Client and server generate session keys
   - Uses asymmetric encryption (RSA, ECDHE)

5. **Finished Messages**
   - Both parties send encrypted "Finished" message
   - Confirms handshake success

6. **Secure Communication**
   - All data encrypted with session keys
   - Uses symmetric encryption (AES, ChaCha20)

For detailed handshake flow: [[networking/tls-handshake|TLS Handshake]]

---

## TLS Components

### Certificates

**X.509 Certificates** contain:
- Public key
- Subject (domain name, organization)
- Issuer (Certificate Authority)
- Validity period
- Digital signature

**Certificate Types:**
- **Domain Validated (DV)**: Basic verification
- **Organization Validated (OV)**: Organization identity verified
- **Extended Validation (EV)**: Highest level of validation

### Certificate Authorities (CAs)

Trusted third parties that issue certificates:
- Let's Encrypt (free, automated)
- DigiCert
- GlobalSign
- Sectigo
- GoDaddy

### Cipher Suites

A cipher suite defines:
- Key exchange algorithm (ECDHE, RSA)
- Authentication algorithm (RSA, ECDSA)
- Encryption algorithm (AES, ChaCha20)
- MAC algorithm (SHA256, SHA384)

Example: `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`

---

## TLS 1.3 Improvements

**Key Changes:**
- Faster handshake (1-RTT, 0-RTT possible)
- Removed insecure cipher suites
- Mandatory Perfect Forward Secrecy
- Encrypted handshake messages
- Simplified negotiation

**Performance:**
- Reduced latency
- Better security by default
- Removed legacy algorithms

---

## Testing TLS Configuration

**OpenSSL Testing:**
```bash
# Test TLS connection
openssl s_client -connect example.com:443

# Test specific TLS version
openssl s_client -connect example.com:443 -tls1_2
openssl s_client -connect example.com:443 -tls1_3

# Check certificate
openssl s_client -connect example.com:443 -showcerts

# Test cipher suites
openssl s_client -connect example.com:443 -cipher 'ECDHE-RSA-AES256-GCM-SHA384'
```

**Online Testing Tools:**
- SSL Labs SSL Test: https://www.ssllabs.com/ssltest/
- ImmuniWeb SSL Security Test
- High-Tech Bridge SSL/TLS Scanner

---

## Common TLS Issues

**Certificate Problems:**
- Expired certificates
- Hostname mismatch
- Self-signed certificates
- Untrusted certificate authority
- Incomplete certificate chain

**Configuration Issues:**
- Weak cipher suites enabled
- Outdated TLS versions
- Missing intermediate certificates
- Incorrect SNI configuration

**Troubleshooting:**
```bash
# Check certificate expiration
echo | openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -noout -dates

# Verify certificate chain
openssl s_client -connect example.com:443 -showcerts

# Check supported protocols
nmap --script ssl-enum-ciphers -p 443 example.com
```

---

## Best Practices

**Server Configuration:**
- Use TLS 1.2 and TLS 1.3 only
- Disable SSL 2.0, SSL 3.0, TLS 1.0, TLS 1.1
- Use strong cipher suites (AEAD ciphers preferred)
- Enable Perfect Forward Secrecy (ECDHE, DHE)
- Use 2048-bit or larger RSA keys
- Consider ECDSA certificates for better performance
- Enable HSTS (HTTP Strict Transport Security)
- Implement OCSP stapling
- Use strong Diffie-Hellman parameters

**Certificate Management:**
- Automate certificate renewal
- Monitor certificate expiration
- Use CAA DNS records
- Maintain complete certificate chain
- Secure private keys
- Consider Certificate Transparency logging

**Client Configuration:**
- Verify server certificates
- Pin certificates for critical connections
- Keep CA trust store updated
- Implement certificate revocation checking

---

## Related Topics

- [[networking/tls-handshake|TLS Handshake]] - Detailed handshake process
- [[misc/ssl-certs|SSL Certificates]] - Certificate management
- [[misc/ssl-security|SSL Security]] - Security best practices
- [[tools/openssl|OpenSSL]] - TLS toolkit
- [[networking/dns-encryption|DNS Encryption]] - DNS over TLS/HTTPS

---

## Additional Resources

- [RFC 8446 - TLS 1.3](https://tools.ietf.org/html/rfc8446)
- [Mozilla SSL Configuration Generator](https://ssl-config.mozilla.org/)
- [OWASP TLS Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html)