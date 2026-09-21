---
name: fusion-lab
description: End-to-end VMware Fusion workflows that combine several skills: build a new VM from an ISO, bake a golden template, stamp out N lab VMs, checkpoint and roll back a lab, patch with rollback, and tear a lab down. Use for datacenter-style tasks like "spin up 3 rocky hosts", "prepare a test lab", "patch with a safety net", "reset the lab".
---

# Fusion lab workflows

Each workflow is an ordered list. Each step names the skill that holds the exact command. Confirm the plan with the user before step 1.

## W1. Build a new VM from ISO

1. `fusion-inventory`: confirm the name is unused.
2. `fusion-template`: `VM Create -n -d -c <guestId>`.
3. `fusion-resources`: set vCPU and memory.
4. `fusion-storage`: `Disk Create`, attach disk, mount ISO.
5. `fusion-network`: pick NAT, host-only, or bridged.
6. `fusion-power`: start. Use `fusion-guest` `MKS captureScreenshot` and `sendKeySequence` to drive an unattended install.
7. `fusion-storage`: eject ISO. `fusion-snapshot`: take `base-install`.

## W2. Golden template and N clones

1. Prepare the source VM (updates, Tools, cleanup, clear machine-id and SSH host keys via `fusion-guest`).
2. `fusion-power`: stop. `fusion-snapshot`: take `golden`.
3. `fusion-template`: linked clones from `golden` for fast disposable labs, or full clones or `VMTemplate Create` for durable ones.
4. For each clone: `fusion-network` (check MAC and IP), set hostname via `fusion-guest`, then `fusion-fleet` start.
5. `fusion-inventory`: `fusion-ls '<prefix>*'` to verify IPs.

## W3. Patch with rollback

1. `fusion-fleet`: snapshot with `-m` semantics per VM if memory state matters, else stop then snapshot.
2. Patch via `fusion-guest` or SSH.
3. Verify (`fusion-guest` `Guest query`, service checks).
4. Pass: delete the checkpoint (ask first). Fail: `fusion-snapshot` revert to the uid and report.

## W4. Reset lab to baseline

1. List targets and their baseline snapshot uids (`Snapshot query`).
2. `fusion-power`: stop each.
3. Confirm with the user, then `Snapshot Revert <uid>` for each.
4. `fusion-fleet` start in tier order.

## W5. Teardown

1. Show the VM list and disk footprint (`fusion-health`).
2. Confirm. `fusion-power` hard or soft stop, then `vmrun deleteVM` per VM. Templates and parent VMs of linked clones stay unless the user names them.

## Rules

- Pick VM name prefixes per lab (`lab1-web01`) so `fusion-fleet` globs isolate one lab.
- Verify with evidence after each phase (`fusion-ls`, `Power query`, `Guest query`) before moving on.
- Never delete a snapshot or VM that a linked clone depends on.
