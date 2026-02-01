# Configuration

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

