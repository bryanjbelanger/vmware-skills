---
name: fusion-inventory
description: List, find, and identify VMware Fusion VMs and resolve a VM name to its .vmx path. Use first whenever a task names a VM, asks what VMs exist, what is running, or needs a VM's IP, CPU, memory, or snapshot count. Also holds the vmcli conventions every other fusion-* skill relies on.
---

# Fusion inventory and conventions

vmcli has no "list VMs" command. Every vmcli call needs the `.vmx` path, so resolve names here first.

## Scripts (read-only)

- `scripts/fusion-ls [name-glob]` prints name, power, vCPU, memory, IP, snapshot count.
- `scripts/fusion-vmx [name-glob]` prints matching `.vmx` paths. Use it to build loops.
- Search roots: `$FUSION_VM_ROOTS` (colon-separated) or `~/Virtual Machines.localized`.

## Conventions for all fusion-* skills

- Syntax: `vmcli "<vmx>" <Module> <Command> [opts] [args]`. Always quote the path (it contains spaces).
- Every module has `query`. Use `vmcli "<vmx>" <Module> <Command> --help` for exact options.
- Running VMs: `vmrun list`. Guest IP: `vmrun getGuestIPAddress "<vmx>"`.
- `Chipset query` returns 0 for memory and CPU on a running VM. Read `memsize` and `numvcpus` from the `.vmx`.
- Hardware edits (CPU, memory, NIC, disk) need the VM powered off unless a command says otherwise.
- Disk, network, and HBA commands address devices by label: `scsi0:0`, `sata0:0`, `ethernet0`.
- Guest commands need VMware Tools running and a guest username and password. Never write passwords into files or commit them. Read them from the environment or ask.
- Confirm with the user before destructive actions: hard power off, snapshot delete or revert, disk purge, VM delete.

## Related skills

Power: fusion-power. Snapshots: fusion-snapshot. CPU, memory, disk size: fusion-resources. NICs and host networks: fusion-network. Run commands in guest: fusion-guest. Disks, ISOs, shared folders: fusion-storage. Create, clone, templates: fusion-template. Many VMs at once: fusion-fleet. Whole lab workflows: fusion-lab. Capacity and health: fusion-health.
