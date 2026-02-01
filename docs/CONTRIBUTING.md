# Contributing

Issues and PRs are welcome, but the design intentionally prioritizes:

- clarity over performance
- minimal dependencies
- step-by-step milestones

Please include:

- a short problem statement
- reproduction steps (pcap preferred)
- expected vs actual behavior
- environment details (macOS version, interface type)

## Git branch rule

- Create branches from `main`. Do not commit directly to `main` (use PRs).
- Use lowercase `kebab-case` names and keep them short but specific.
- Branch name format: `<type>/<topic>` (optional: `-#<issue>` at the end)
  - `feature/` : new functionality (e.g., `feature/arp-reply`)
  - `fix/` : bug fixes (e.g., `fix/ip4-checksum`)
  - `refactor/` : internal refactors (e.g., `refactor/tcp-state-machine`)
  - `docs/` : documentation only (e.g., `docs/update-readme`)
  - `test/` : tests only (e.g., `test/hkdf-vectors`)
  - `chore/` : tooling/maintenance (e.g., `chore/add-gitignore`)
  - `spike/` : experiments/prototypes (e.g., `spike/tls-record-layer`)
- Prefer one logical change per branch to keep review and rollback simple.

## File / directory rule

- Put implementation code under `src/` (avoid top-level code files).
- Prefer a module-per-directory layout aligned to the architecture, e.g.:
  - `src/link/`, `src/eth/`, `src/arp/`, `src/ip4/`, `src/icmp/`, `src/tcp/`, `src/crypto/`, `src/tls13/`, `src/http/`, `src/app/`
- Naming:
  - Directories: lowercase (e.g., `ip4`, `tls13`)
  - Files: lowercase `snake_case` (e.g., `tcp_state.c`, `tcp_state.h`)
- Keep public APIs small: expose only what higher layers need; avoid cross-layer back-references (e.g., `eth/` should not depend on `tcp/`).
- Generated artifacts (e.g., `build/`, `.o`, `.dSYM`, `.pcap`) should not be committed.

