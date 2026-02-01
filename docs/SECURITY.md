# Security Notes

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

