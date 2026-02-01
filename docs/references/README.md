# References (RFCs, specs, vectors)

This folder is a curated index of documents used while building this project.

Notes:

- Prefer links + short notes over copying full documents into the repo.
- Do not commit copyrighted PDFs/HTML dumps (RFCs are generally OK to link to).

## RFCs

Add entries as you use them:

| ID | Topic | Used for | Notes |
|---:|---|---|---|
| RFC 791 | IPv4 | `ip4/` | header format, checksum |
| RFC 792 | ICMP | `icmp/` | echo request/reply |
| RFC 826 | ARP | `arp/` | request/reply, cache semantics |
| RFC 9293 | TCP | `tcp/` | state machine, segments, RTO basics |
| RFC 8446 | TLS 1.3 | `tls13/` | record layer, handshake, key schedule |

## Crypto / test vectors

| Document | Used for | Notes |
|---|---|---|
| FIPS 180-4 | SHA-256 | KAT vectors |
| RFC 5869 | HKDF | Extract/Expand vectors |
| RFC 4231 | HMAC-SHA256 | test vectors |
| NIST CAVP | SHA/AES-GCM | known-answer tests (pick a set and pin it) |
| RFC 7539 | ChaCha20-Poly1305 | vectors (if chosen) |

## Tools / docs

- BPF / macOS: add links you rely on (manpages, Apple docs, etc.)
- Wireshark, tcpdump, ifconfig/netstat: command references you commonly use
