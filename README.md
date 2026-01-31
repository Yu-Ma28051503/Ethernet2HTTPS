# Ethernet2HTTPS

> Userspace “toy” HTTPS stack on macOS: L2 (Ethernet) → L3 (IPv4) → TCP → **TLS 1.3 (with self-implemented crypto)** → HTTP/1.1  
> **Goal:** communicate over a **physical NIC** on an M4 Mac mini (macOS) using **BPF** for L2 I/O.

---

## Status

- Current stage: **[planned / in progress / working / paused]**
- Latest milestone achieved: **[e.g., ARP reply works / ICMP ping works / TCP handshake works / TLS record works]**
- Next milestone: **[e.g., minimal TCP data path / TLS1.3 PSK-only handshake]**

> ⚠️ This is a learning/research project. It is **not production-ready** and **not secure** for real-world use.

---

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

---

## Non-Goals / Out of Scope (for now)

- Full TCP (SACK, full congestion control, window scaling, etc.)
- IPv6
- IP fragmentation/reassembly
- Full TLS interoperability with mainstream browsers
- HTTP/2, HTTP/3
- Real certificate validation, OCSP, CT, etc.
- Performance tuning / zero-copy optimizations

---

## Architecture Overview

High-level layering:

- `link/`    : L2 I/O backend (BPF) — `recv_frame()` / `send_frame()`
- `eth/`     : Ethernet parsing/building
- `arp/`     : ARP cache + request/reply
- `ip4/`     : IPv4 parsing/building + checksum
- `icmp/`    : ICMP echo (bring-up)
- `tcp/`     : TCP state machine + retransmit timer
- `crypto/`  : primitives (SHA-256, HMAC, HKDF, AEAD, optional ECC/signatures)
- `tls13/`   : TLS 1.3 record + handshake + key schedule (uses `crypto/`)
- `http/`    : minimal HTTP/1.1 handler
- `app/`     : main loop + config + logging

Event loop:
- single-threaded, event-driven (e.g., kqueue)
- timers for ARP expiry, TCP RTO, TLS handshake timeouts

---

## Prerequisites

- macOS on Apple Silicon (tested on **M4 Mac mini**)
- Xcode Command Line Tools
- root privileges (or appropriate permissions) for `/dev/bpf*`
- Tools for debugging:
	- `tcpdump` / Wireshark
	- `ifconfig`, `netstat`

---

## Build

```bash
# example (adjust for your build system)
mkdir -p build
cc -O2 -Wall -Wextra -o build/app src/main.c
```

## Run

> ⚠️ Recommended: test in an **isolated L2 segment** (dedicated switch/VLAN or direct link) to avoid ARP/IP conflicts.  
> BPF access typically requires `sudo` on macOS.

### 1) Identify your network interface
```bash
ifconfig
```

### 2) Run the stack (example)

```bash
sudo ./build/app \
  --iface en0 \
  --ip 192.0.2.10 \
  --mac 02:00:00:00:00:01 \
  --listen 443
```

### 3) Observe traffic

```bash
sudo tcpdump -i en0 -n -e "arp or (ip and host 192.0.2.10)"
```


## Configuration

Command-line flags (example):

## Configuration

Command-line flags (example):

- `--iface <ifname>` : network interface (e.g., `en0`)
- `--mac <xx:xx:xx:xx:xx:xx>` : L2 address used by the stack
- `--ip <a.b.c.d>` : IPv4 address used by the stack
- `--netmask <a.b.c.d>` : IPv4 netmask (optional; default may be /24)
- `--gw <a.b.c.d>` : default gateway (optional; set only if routing is implemented)
- `--listen <port>` : TCP listen port (e.g., `443`)
- `--psk <hex>` : TLS PSK (for PSK-only mode; **do not reuse** in real environments)
- `--psk-id <string>` : TLS PSK identity (optional, for clarity/testing)
- `--log <level>` : `trace|debug|info|warn|error`
- `--pcap <path>` : write captured/processed frames to a pcap file (optional, if you add this feature)
- `--mtu <bytes>` : override MTU (optional; helpful for testing)
- `--dry-run` : parse/validate only, do not transmit (optional, if you add this feature)

Example:
```bash
sudo ./build/app \
  --iface en0 \
  --ip 192.0.2.10 \
  --mac 02:00:00:00:00:01 \
  --listen 443 \
  --log debug
```

## Testing & Debugging

### Milestone tests

- L2: receive frames + filter (BPF)
- ARP: respond to ARP request for --ip
- ICMP: respond to ping <ip>
- TCP: SYN/SYN-ACK/ACK visible in tcpdump
- Crypto: known-answer tests (KAT) + property tests (where applicable)
- TLS: record encrypt/decrypt unit tests (known-answer tests)
- HTTP: curl against plaintext first, then TLS

### Crypto test strategy (recommended)]
- Implement each primitive with official test vectors:
	- SHA-256 (FIPS 180-4 vectors)
	- HMAC-SHA256 (RFC 4231 vectors)
	- HKDF (RFC 5869 vectors)
	- AES-GCM (NIST CAVP vectors) or ChaCha20-Poly1305 (RFC 7539 vectors)
- Add negative tests:
	- tag verification failures
	- invalid lengths / malformed inputs

### Suggested commands

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

> Note: mainstream browser interoperability may require additional TLS features (SNI/ALPN/certs).

## Security Notes

This project is educational and intentionally implements parts of a network stack and cryptography from scratch.
Expect missing defenses and security properties until explicitly implemented and tested:

- **Packet parsing is adversarial input**: every layer must validate lengths, bounds, and invariants.
- **TCP hardening is incomplete** (at least initially):
  - no SYN flood protection
  - weak/absent sequence-number randomization
  - limited retransmission and no congestion control
  - easy to break with malformed or out-of-order segments
- **Cryptographic implementations are high-risk**:
  - constant-time behavior may be incomplete
  - side-channel resistance is not guaranteed
  - nonce/sequence misuse will catastrophically break AEAD security
  - RNG quality is critical
- **TLS 1.3 interoperability will be limited** until extensions and validation are added.
- **Do not expose to the public Internet.**
  - Use an isolated L2 segment/VLAN or a controlled lab environment.
  - Treat all keys and PSKs as test-only.

---

## Roadmap

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

---

## Contributing

- Issues and PRs are welcome, but the design intentionally prioritizes:
	- clarity over performance
	- minimal dependencies
	- step-by-step milestones
- Please include:
	- a short problem statement
	- reproduction steps (pcap preferred)
	- expected vs actual behavior
	- environment details (macOS version, interface type)

---

## License

Choose one and include a `LICENSE` file:

- MIT
- Apache-2.0
- BSD-2-Clause / BSD-3-Clause

---

## Acknowledgements / References

- Packet capture / BPF tooling
- TCP/IP specifications (RFCs) and reference implementations
- TLS 1.3 specification (RFC 8446)
- HKDF (RFC 5869), HMAC (RFC 2104 / RFC 4231), AEAD specs / vectors
- NIST CAVP test vectors (for SHA/AES-GCM)
- Wireshark for traffic inspection

(Add specific papers, repos, and links you used here.)

---

## Author
- UMA(ゆーま)
