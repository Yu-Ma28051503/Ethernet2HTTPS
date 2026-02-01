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

## Docs

- Architecture: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)
- Roadmap / planned features / non-goals: [docs/ROADMAP.md](docs/ROADMAP.md)
- Configuration (flags): [docs/CONFIG.md](docs/CONFIG.md)
- Testing & debugging: [docs/TESTING.md](docs/TESTING.md)
- Security notes: [docs/SECURITY.md](docs/SECURITY.md)
- Contributing (branch + file/directory rules): [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md)
- References (RFCs, specs, vectors): [docs/references/README.md](docs/references/README.md)

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
