# OpenSSL

**OpenSSL** is a robust, commercial-grade toolkit for the Transport Layer Security (TLS) and Secure Sockets Layer (SSL) protocols. It's also a general-purpose cryptography library.

Project Homepage: [OpenSSL](https://www.openssl.org/)
Documentation: [OpenSSL Documentation](https://www.openssl.org/docs/)

---

## Installation

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install openssl
```

**macOS:**
```bash
brew install openssl
```

**Verify Installation:**
```bash
openssl version
openssl version -a
```

---

## Certificate Operations

### Generate Private Key

**RSA Key:**
```bash
# Generate 2048-bit RSA private key
openssl genrsa -out private.key 2048

# Generate 4096-bit RSA private key
openssl genrsa -out private.key 4096

# Generate encrypted private key
openssl genrsa -aes256 -out private.key 2048
```

**ECDSA Key (Elliptic Curve):**
```bash
# Generate ECDSA private key
openssl ecparam -genkey -name secp384r1 -out private.key

# List available curves
openssl ecparam -list_curves
```

### Generate Certificate Signing Request (CSR)

```bash
# Generate CSR from existing private key
openssl req -new -key private.key -out request.csr

# Generate CSR with new private key
openssl req -new -newkey rsa:2048 -nodes -keyout private.key -out request.csr

# Generate CSR non-interactively
openssl req -new -key private.key -out request.csr \
  -subj "/C=US/ST=State/L=City/O=Organization/CN=example.com"
```

### Generate Self-Signed Certificate

```bash
# Generate self-signed certificate (1 year validity)
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout private.key -out certificate.crt -days 365

# Generate from existing private key
openssl req -x509 -key private.key -out certificate.crt -days 365

# Generate with specific subject
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout private.key -out certificate.crt -days 365 \
  -subj "/C=US/ST=State/L=City/O=Organization/CN=example.com"
```

### View Certificate Information

```bash
# View certificate details
openssl x509 -in certificate.crt -text -noout

# View certificate dates
openssl x509 -in certificate.crt -noout -dates

# View certificate subject
openssl x509 -in certificate.crt -noout -subject

# View certificate issuer
openssl x509 -in certificate.crt -noout -issuer

# Check certificate fingerprint
openssl x509 -in certificate.crt -noout -fingerprint
```

### View CSR Information

```bash
# View CSR details
openssl req -in request.csr -text -noout

# Verify CSR signature
openssl req -in request.csr -verify -noout
```

### View Private Key Information

```bash
# View private key details
openssl rsa -in private.key -text -noout

# Check if private key is encrypted
openssl rsa -in private.key -check

# Remove passphrase from private key
openssl rsa -in encrypted.key -out decrypted.key
```

---

## Certificate Conversions

### Convert Formats

**PEM to DER:**
```bash
openssl x509 -in certificate.pem -outform DER -out certificate.der
```

**DER to PEM:**
```bash
openssl x509 -in certificate.der -inform DER -out certificate.pem
```

**PEM to PKCS#12:**
```bash
openssl pkcs12 -export -in certificate.crt -inkey private.key \
  -out certificate.pfx -name "My Certificate"
```

**PKCS#12 to PEM:**
```bash
# Extract certificate
openssl pkcs12 -in certificate.pfx -clcerts -nokeys -out certificate.crt

# Extract private key
openssl pkcs12 -in certificate.pfx -nocerts -nodes -out private.key
```

---

## TLS/SSL Testing

### Test TLS Connection

```bash
# Connect to HTTPS server
openssl s_client -connect example.com:443

# Test specific TLS version
openssl s_client -connect example.com:443 -tls1_2
openssl s_client -connect example.com:443 -tls1_3

# Show all certificates in chain
openssl s_client -connect example.com:443 -showcerts

# Test with SNI (Server Name Indication)
openssl s_client -connect example.com:443 -servername example.com

# Check certificate expiration
echo | openssl s_client -connect example.com:443 2>/dev/null | \
  openssl x509 -noout -dates
```

### Test Specific Cipher

```bash
# Test specific cipher suite
openssl s_client -connect example.com:443 \
  -cipher 'ECDHE-RSA-AES256-GCM-SHA384'

# List supported ciphers
openssl ciphers -v
```

### Download Server Certificate

```bash
# Download certificate from server
echo | openssl s_client -connect example.com:443 2>/dev/null | \
  openssl x509 -out certificate.crt
```

---

## Hashing and Encoding

### Hash Functions

```bash
# MD5 hash (not recommended for security)
echo -n "text" | openssl md5

# SHA-256 hash
echo -n "text" | openssl sha256

# SHA-512 hash
echo -n "text" | openssl sha512

# Hash a file
openssl sha256 file.txt
```

### Base64 Encoding/Decoding

```bash
# Encode to base64
echo "text" | openssl base64

# Decode from base64
echo "dGV4dAo=" | openssl base64 -d

# Encode file
openssl base64 -in file.txt -out file.b64

# Decode file
openssl base64 -d -in file.b64 -out file.txt
```

---

## Encryption and Decryption

### Symmetric Encryption

**Encrypt File:**
```bash
# AES-256-CBC encryption
openssl enc -aes-256-cbc -salt -in file.txt -out file.enc

# With password from command line (not recommended)
openssl enc -aes-256-cbc -salt -in file.txt -out file.enc -k password

# With password from file
openssl enc -aes-256-cbc -salt -in file.txt -out file.enc -pass file:password.txt
```

**Decrypt File:**
```bash
# Decrypt file
openssl enc -aes-256-cbc -d -in file.enc -out file.txt

# With password
openssl enc -aes-256-cbc -d -in file.enc -out file.txt -k password
```

### Public Key Encryption

**Encrypt with Public Key:**
```bash
# Extract public key from certificate
openssl x509 -in certificate.crt -pubkey -noout > public.key

# Encrypt file with public key
openssl rsautl -encrypt -pubin -inkey public.key \
  -in file.txt -out file.enc
```

**Decrypt with Private Key:**
```bash
# Decrypt file with private key
openssl rsautl -decrypt -inkey private.key \
  -in file.enc -out file.txt
```

---

## Digital Signatures

**Sign File:**
```bash
# Create signature
openssl dgst -sha256 -sign private.key -out signature.sig file.txt
```

**Verify Signature:**
```bash
# Verify signature
openssl dgst -sha256 -verify public.key -signature signature.sig file.txt
```

---

## Certificate Verification

**Verify Certificate:**
```bash
# Verify certificate against CA
openssl verify -CAfile ca.crt certificate.crt

# Verify certificate chain
openssl verify -CAfile ca.crt -untrusted intermediate.crt certificate.crt

# Check certificate purpose
openssl x509 -in certificate.crt -noout -purpose
```

**Match Private Key and Certificate:**
```bash
# Get modulus from certificate
openssl x509 -noout -modulus -in certificate.crt | openssl md5

# Get modulus from private key
openssl rsa -noout -modulus -in private.key | openssl md5

# They should match
```

---

## Diffie-Hellman Parameters

```bash
# Generate DH parameters (2048-bit)
openssl dhparam -out dhparam.pem 2048

# Generate DH parameters (4096-bit, more secure but slower)
openssl dhparam -out dhparam.pem 4096

# View DH parameters
openssl dhparam -in dhparam.pem -text -noout
```

---

## Random Data Generation

```bash
# Generate random bytes
openssl rand 32

# Generate random base64 string
openssl rand -base64 32

# Generate random hex string
openssl rand -hex 32

# Save random data to file
openssl rand -out random.bin 1024
```

---

## Common Use Cases

### Create CA and Sign Certificate

**Create CA:**
```bash
# Generate CA private key
openssl genrsa -aes256 -out ca.key 4096

# Generate CA certificate
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 \
  -out ca.crt -subj "/CN=My CA"
```

**Sign Certificate:**
```bash
# Create server private key and CSR
openssl req -newkey rsa:2048 -nodes -keyout server.key -out server.csr

# Sign CSR with CA
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key \
  -CAcreateserial -out server.crt -days 365 -sha256
```

### Bundle Certificate Chain

```bash
# Combine certificate and chain
cat certificate.crt intermediate.crt ca.crt > fullchain.pem

# Create PEM bundle with private key
cat certificate.crt private.key > bundle.pem
```

---

## Related Topics

- [[networking/tls|TLS]] - Transport Layer Security
- [[misc/ssl-certs|SSL Certificates]] - Certificate management
- [[misc/ssl-security|SSL Security]] - Security best practices
- [[networking/tls-handshake|TLS Handshake]] - TLS connection process

---

## Best Practices

- Use at least 2048-bit RSA keys (4096-bit preferred)
- Consider ECDSA for better performance
- Keep private keys secure and encrypted
- Use strong passphrases for encrypted keys
- Regularly rotate certificates before expiration
- Use SHA-256 or stronger for hashing
- Avoid MD5 and SHA-1 (deprecated)
- Back up private keys securely
- Use certificate pinning for critical applications
- Monitor certificate expiration dates

---

## Security Warnings

⚠️ **Never** share private keys
⚠️ **Never** commit private keys to version control
⚠️ Use strong passphrases for encrypted keys
⚠️ Verify certificates from untrusted sources
⚠️ Keep OpenSSL updated for security patches