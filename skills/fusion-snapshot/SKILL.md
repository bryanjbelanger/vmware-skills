---
name: fusion-snapshot
description: Take, list, revert, delete, or clone VMware Fusion VM snapshots with vmcli. Use for checkpoints before risky changes, rollbacks, and cloning from a snapshot. For snapshots across many VMs use fusion-fleet.
---

# Fusion snapshots

Let `V` be the `.vmx` path. Snapshots are addressed by **uid**, not name.

| Goal | Command |
|---|---|
| List (get uids) | `vmcli "$V" Snapshot query` or `vmrun listSnapshots "$V"` |
| Take | `vmcli "$V" Snapshot Take [-m] [-d "desc"] "<name>"` |
| Revert | `vmcli "$V" Snapshot Revert <uid>` |
| Delete | `vmcli "$V" Snapshot Delete [-d] <uid>` |
| Clone from snapshot | `vmcli "$V" Snapshot Clone [-l] [-o] <uid> <filePath> "<name>"` |

## Rules

- `-m` includes memory state. Only meaningful on a running VM. Without it the snapshot is disk-only.
- Snapshot names must not contain `/`.
- `Take` does not print the uid. Run `Snapshot query` afterward to find it.
- `Revert` discards all current state after the snapshot. `Delete -d` also deletes child snapshots. Confirm both with the user, and show which uid and name you resolved.
- Delete requires the VM powered off or suspended.
- `Clone -l` makes a linked clone that depends on the parent disk. Do not move or delete the parent.
- Long snapshot chains slow the VM. Prefer a few named baselines over many.
