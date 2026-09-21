---
name: fusion-guest
description: Run commands, transfer files, list processes, read guest facts, take screenshots, and send keystrokes inside a running VMware Fusion guest with vmcli. Requires VMware Tools and guest credentials. Use for "run X in the VM", "copy this file to the VM", "what's running in the guest", "screenshot the VM".
---

# Fusion guest operations

Let `V` be the `.vmx` path. VM must be running, Tools must be up. Check with `vmcli "$V" Tools Query` and `vmcli "$V" Guest query`.

Credentials: use `$GUEST_USER` and `$GUEST_PASS` from the environment, or ask. Never store or echo passwords.

| Goal | Command |
|---|---|
| Facts (OS, IP, disks, uptime) | `vmcli "$V" Guest query` |
| Run a program | `vmcli "$V" Guest run -u "$GUEST_USER" -p "$GUEST_PASS" /bin/bash -c "<cmd>"` |
| Run and detach | add `-nw` |
| Windows GUI app | add `-i -aw` |
| Host to guest | `Guest copyTo -u .. -p .. [-o] <hostPath> <guestPath>` |
| Guest to host | `Guest copyFrom -u .. -p .. <guestPath> <hostPath>` |
| Processes | `Guest ps -u .. -p ..` (`-pid`, `-f` for format) |
| Kill | `Guest kill -u .. -p .. <pid>` |
| Files | `Guest ls`, `mkdir`, `rm`, `rmdir`, `mv`, `createTempFile` (same `-u -p`) |
| Env vars | `Guest env -u .. -p ..` |
| Screenshot | `vmcli "$V" MKS captureScreenshot <file.png>` |
| Keystrokes | `vmcli "$V" MKS sendKeySequence "<seq>"` (works before an OS or Tools exist) |

## Rules

- `Guest run` does not use a shell. Wrap pipelines and redirects in `/bin/bash -c "..."` (Linux) or `cmd.exe /c` (Windows).
- Program paths must be absolute.
- If guest ops fail, check `Tools Query` first. Freshly booted guests need seconds to start Tools.
- Prefer SSH when the guest already has it and you have keys. Use these commands for bootstrap, recovery, and hosts without network.
- Ask the user before any guest command that deletes data recursively or changes disks, firewall, or accounts.
