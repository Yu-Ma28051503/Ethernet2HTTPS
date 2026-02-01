# Roadmap

## Features (Planned)

### Networking
- [ ] Ethernet II (L2 framing)
- [ ] ARP (IPv4) + cache
- [ ] IPv4 (header parsing/building, checksum)
- [ ] ICMP Echo (for bring-up testing)
- [ ] TCP (single connection, minimal retransmit, no congestion control at first)

### TLS 1.3
- [ ] Record Layer (AEAD encrypt/decrypt)
- [ ] Key schedule (HKDF)
- [ ] Handshake:
	- [ ] PSK-only (first target)
  	- [ ] Certificate-based (later)
- [ ] Cipher suites (initially fixed to 1)
- [ ] Transcript hash + Finished verification

### Cryptography (Self-Implemented)
> The crypto layer is intentionally implemented in-house for learning. This is high-risk and requires extensive test vectors.

#### Hash / MAC / KDF
- [ ] SHA-256 (required for many TLS 1.3 profiles)
- [ ] HMAC-SHA256
- [ ] HKDF (Extract/Expand, RFC 5869)

#### AEAD (TLS 1.3 Record Protection)
- [ ] AES-128-GCM **or** ChaCha20-Poly1305 (start with one)
- [ ] Nonce construction (static IV + per-record sequence number XOR)
- [ ] Additional Data (TLSCiphertext header)

#### Public-key (optional path, later)
- [ ] ECDHE key exchange (e.g., X25519 or P-256)
- [ ] Signature verification (if certificate-based mode is implemented)
	- [ ] ECDSA(P-256/SHA-256) or Ed25519 (choose one)
- [ ] Minimal X.509 parsing (only what you need)

#### Randomness
- [ ] CSPRNG integration (OS entropy source)
- [ ] Deterministic test mode (fixed seeds) for reproducible unit tests

## Non-Goals / Out of Scope (for now)

- Full TCP (SACK, full congestion control, window scaling, etc.)
- IPv6
- IP fragmentation/reassembly
- Full TLS interoperability with mainstream browsers
- HTTP/2, HTTP/3
- Real certificate validation, OCSP, CT, etc.
- Performance tuning / zero-copy optimizations

## Milestones

1. BPF bring-up: RX/TX + minimal filter
2. ARP reply + cache
3. IPv4 + ICMP echo reply
4. TCP listen + handshake (single connection)
5. TCP data path + minimal retransmit
6. HTTP/1.1 plaintext minimal server

### Crypto-first milestones
7. SHA-256 → HMAC → HKDF (with test vectors)
8. AEAD (AES-128-GCM **or** ChaCha20-Poly1305) (with test vectors)
9. Deterministic test harness (fixed seeds) + fuzz targets for parsers (optional but recommended)

### TLS milestones
10. TLS 1.3 record layer (protect/unprotect records)
11. TLS 1.3 key schedule + transcript hash + Finished verification
12. TLS 1.3 PSK-only handshake
13. HTTPS (HTTP over TLS)

### Optional / advanced
14. ECDHE (X25519 or P-256) + certificate-based TLS
15. Minimal X.509 parsing + signature verification (one algorithm only)
16. Better TCP correctness (reassembly, out-of-order, window management)
17. Hardening (rate limiting, input sanitization, timeouts)
18. Interop work (SNI/ALPN, more extensions/ciphers)

