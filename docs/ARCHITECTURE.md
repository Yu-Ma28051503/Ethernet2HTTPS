# Architecture

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

