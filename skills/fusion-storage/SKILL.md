---
name: fusion-storage
description: Manage VMware Fusion VM disks, CD/DVD ISO attachment, shared folders (HGFS), and disk controllers with vmcli. Use for adding a disk, mounting an ISO, sharing a host folder, disk modes, or removing a disk.
---

# Fusion storage

Let `V` be the `.vmx` path. VM off for adding or removing devices. Resizing lives in fusion-resources.

| Goal | Command |
|---|---|
| Disks and CD-ROMs | `vmcli "$V" Disk query` (labels like `sata0:0`) |
| New vmdk | `vmcli "$V" Disk Create -f <full/path.vmdk> -a lsilogic -s 20GB -t 0` |
| Attach a disk | `vmcli "$V" Disk SetBackingInfo <label> disk <path.vmdk> false`, then `Disk SetPresent <label> true` |
| Mount ISO | `vmcli "$V" Disk SetBackingInfo sata0:1 cdrom_image <iso> false`, then `Disk ConnectionControl --help` to connect |
| Eject ISO | `Disk SetBackingInfo sata0:1 cdrom_atapi "" false` |
| Detach | `Disk SetPresent <label> false` |
| Remove device config | `Disk Purge <label>` (config only, confirm with user) |
| Mode | `Disk SetMode <label> <mode>` (persistent or independent) |
| Shared folders | `HGFS query`, `HGFS SetPresent <shareLabel> true`, `SetHostPath`, `SetGuestName`, `SetEnabled`, `SetWriteAccess` |
| HBAs | `Sata query`, `Nvme query`, `Sata SetPresent`, `Sata FindFirstFree` |

## Rules

- `-t` disk type: 0 growable single file, 1 growable split, 2 preallocated, 3 preallocated split. Use 0 unless asked, and 2 for benchmarks.
- `Disk Create` only makes the file. It is not attached until `SetBackingInfo` and `SetPresent`.
- Read the current label and `backingType` in `Disk query` and reuse its spelling before writing. The ISO and eject `backingType` strings above are inferred from query output and not yet tested.
- `Disk Purge` removes VM configuration, not the vmdk file. Deleting the file is a separate, confirmed action.
- Shared folders need Tools in the guest. Never share `/` or the whole home directory.
