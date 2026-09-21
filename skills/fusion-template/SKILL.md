---
name: fusion-template
description: Create new VMware Fusion VMs, clone VMs, and build or deploy VM templates (.vmtx) with vmcli and vmrun. Use for "make a new VM", "clone this VM", "turn this into a template", "deploy N copies".
---

# Fusion provisioning

| Goal | Command |
|---|---|
| New empty VM | `vmcli VM Create -n <name> -d "<dir>" -c <guestId>` (e.g. `-c ubuntu-64`, `other5xlinux-64`) |
| Full clone | `vmrun clone "$V" "<dest.vmx>" full -cloneName=<name>` |
| Linked clone | `vmrun clone "$V" "<dest.vmx>" linked -snapshot=<snapname> -cloneName=<name>` |
| Clone from snapshot | `vmcli "$V" Snapshot Clone [-l] <uid> <destPath> "<name>"` |
| Make template | `vmcli "$V" VMTemplate Create -p "<dir>/<name>.vmtx" -n "<name>"` |
| Deploy template | `vmcli VMTemplate Deploy -p "<path.vmtx>"` |
| Delete VM | `vmrun deleteVM "$V"` (confirm first) |

## Rules

- Source VM must be off for a full clone or template.
- `VM Create` makes an empty shell. Then add a disk and ISO (fusion-storage), size it (fusion-resources), set the network (fusion-network), and power on to install. Guest ids are the vmx `guestOS` values. Read an existing VM's `guestOS` from its `.vmx` for a known-good spelling.
- Linked clones are quick and small but depend on the parent disk and the named snapshot. Do not delete either.
- Store templates outside the live VM folder, for example `~/Virtual Machines.localized/_templates/`. Keep every file created at template time together in place, or Deploy fails.
- Generalize before templating (clear machine-id, SSH host keys, cloud-init state) via fusion-guest. Clones otherwise share guest identity.
- Use fusion-lab for the full build, baseline, and stamp workflow.
