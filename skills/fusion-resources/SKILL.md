---
name: fusion-resources
description: Change or read CPU count, cores per socket, memory, and virtual disk size of a VMware Fusion VM with vmcli. Use for "give it more RAM", "add vCPUs", "grow the disk", or "what are its specs".
---

# Fusion compute and disk sizing

Let `V` be the `.vmx` path. Power the VM off first (see fusion-power).

| Goal | Command |
|---|---|
| Read specs | `grep -E '^(memsize\|numvcpus\|cpuid.coresPerSocket)' "$V"` |
| vCPUs | `vmcli "$V" Chipset SetVCpuCount <n>` |
| Cores per socket | `vmcli "$V" Chipset SetCoresPerSocket <n>` |
| Threads per vCPU | `vmcli "$V" Chipset SetSimultaneousThreads <n>` |
| Memory | `vmcli "$V" Chipset SetMemSize <MB>` |
| Disk list | `vmcli "$V" Disk query` |
| Grow disk | `vmcli "$V" Disk Extend <diskLabel> <newNumSectors>` |

## Rules

- `Chipset query` reports 0 for a running VM. Read the `.vmx` instead.
- `Disk Extend` takes the **new total size in 512-byte sectors**, not a delta. Example: 60 GB = `60*1024*1024*1024/512` = `125829120`.
- Extend needs no snapshots on the VM. Check `Snapshot query` first.
- Extending the disk does not grow the guest partition or filesystem. Follow up with fusion-guest (`growpart`, `resize2fs`, `xfs_growfs`, or Disk Management).
- Keep memory a multiple of 4 and vCPUs at or below the host's physical cores. Ask before oversizing.
- Extra settings not covered by a module: `vmcli "$V" ConfigParams SetEntry <key> <value>` (edits `.vmx` keys, VM off).
