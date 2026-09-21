---
name: fusion-power
description: Start, stop, suspend, pause, reset, or check the power state of one VMware Fusion VM using vmcli. Use for "boot", "shut down", "power off", "suspend", "restart" a single VM. For several VMs at once use fusion-fleet.
---

# Fusion power

Resolve the `.vmx` path with fusion-inventory. Let `V` be the path.

| Goal | Command |
|---|---|
| State | `vmcli "$V" Power query` (look at `PowerState`) |
| Start | `vmcli "$V" Power Start` (`-p` start paused, `-s` soft) |
| Graceful stop | `vmcli "$V" Power Stop -o trySoft` |
| Require guest shutdown | `vmcli "$V" Power Stop -o requireSoft` |
| Hard off | `vmcli "$V" Power Stop -o hard` |
| Suspend | `vmcli "$V" Power Suspend -o trySoft` |
| Pause / resume | `vmcli "$V" Power Pause` / `Unpause` |
| Reset | `vmcli "$V" Power Reset` |

## Rules

- `-o` takes names only: `trySoft`, `requireSoft`, `configDefault`, `hard`. The help text shows numbers, but the CLI rejects them.
- Default to `trySoft`. Soft stop needs VMware Tools. `hard` can corrupt guest filesystems, so ask first.
- `Start` returns before the guest is usable. To wait for boot, poll `vmrun getGuestIPAddress "$V"` until it prints an IP.
- Stopping before a hardware change: stop, then verify `PowerState: off` before editing.
