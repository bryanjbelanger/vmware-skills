---
name: fusion-health
description: Report VMware Fusion host capacity and guest health: host CPU, memory, and disk headroom, VM disk footprint, running vCPU and memory allocation, and per-guest uptime, IP, and filesystem usage. Use for "how's the lab", "am I out of space or RAM", "which VM is biggest", or before starting many VMs.
---

# Fusion health

Run `scripts/fusion-health` (read-only). It reports:

1. Host CPUs, RAM, and free space on the VM volume.
2. On-disk size of each `.vmwarevm` bundle, largest first.
3. Total vCPUs and memory allocated to running VMs.
4. For each running guest: IP, uptime, and free space per mount (from `Guest query`, no credentials needed).

## Interpreting

- Free space on the VM volume below about 20%: stop and clean up before taking more snapshots or clones.
- Running memory above about 75% of host RAM: warn before starting more VMs. macOS needs headroom.
- Running vCPUs above host CPUs: legal but contended. Mention it.
- Guest mount over 85% used: propose growing the disk (fusion-resources) or cleaning the guest.
- Bundles much larger than their disk size usually carry snapshot chains. Check with `fusion-snapshot`.

## Not covered

Guest CPU and memory load need in-guest commands (`fusion-guest`, for example `uptime`, `free -m`). Power state of every VM is in `fusion-inventory`.
