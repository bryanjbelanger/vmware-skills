---
name: fusion-fleet
description: Run one operation across many VMware Fusion VMs selected by name glob, such as start, stop, suspend, or snapshot all "cis-*" VMs, with dry-run by default. Use for "all VMs", "every cis VM", "shut down the lab", "snapshot everything before patching".
---

# Fusion fleet operations

Script: `scripts/fusion-each [--yes] <name-glob> <action> [arg]`

Actions: `status`, `start`, `stop` (trySoft), `hardstop`, `suspend`, `snapshot <name>`.

- Without `--yes` it prints the plan and changes nothing. Always run the dry run first and show the VM list to the user.
- It skips VMs already in the target state.
- Glob matches VM name, not path. `'*'` is every VM. Names come from fusion-inventory.

## Patterns

- Pre-change checkpoint: `fusion-each --yes 'cis-*' snapshot pre-patch-YYYYMMDD` (disk-only for off VMs, so stop first if you need consistency).
- Ordered start (dependencies): call `fusion-each --yes 'db-*' start`, wait until `vmrun getGuestIPAddress` returns an IP for each, then the next tier.
- Ordered stop is the reverse tier order.
- Anything not in the action list (resize, network, guest commands): loop over `fusion-vmx '<glob>'` and use the matching granular skill per VM.

## Rules

- Confirm the target list before `--yes` on `hardstop`, and on any pattern broader than the user named.
- Failures print `FAILED` for that VM and the loop continues. Report which VMs failed, do not retry blindly.
- Do not run more than a few concurrent starts. Check host headroom with fusion-health first.
