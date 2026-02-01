# Testing & Debugging

## Milestone tests

- L2: receive frames + filter (BPF)
- ARP: respond to ARP request for `--ip`
- ICMP: respond to `ping <ip>`
- TCP: SYN/SYN-ACK/ACK visible in tcpdump
- Crypto: known-answer tests (KAT) + property tests (where applicable)
- TLS: record encrypt/decrypt unit tests (known-answer tests)
- HTTP: curl against plaintext first, then TLS

## Crypto test strategy (recommended)

- Implement each primitive with official test vectors:
	- SHA-256 (FIPS 180-4 vectors)
	- HMAC-SHA256 (RFC 4231 vectors)
	- HKDF (RFC 5869 vectors)
	- AES-GCM (NIST CAVP vectors) or ChaCha20-Poly1305 (RFC 7539 vectors)
- Add negative tests:
	- tag verification failures
	- invalid lengths / malformed inputs

## Suggested commands

```bash
# ARP probing
arp -a

# Ping (ICMP bring-up)
ping 192.0.2.10

# TCP (if plaintext stage exists)
nc -v 192.0.2.10 8080

# TLS handshake testing (later)
# openssl s_client -connect 192.0.2.10:443 -tls1_3
```

Note: mainstream browser interoperability may require additional TLS features (SNI/ALPN/certs).

