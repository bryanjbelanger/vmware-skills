# VMware Fusion skills

Skills for managing VMware Fusion like a datacenter with `vmcli` (plus `vmrun` where vmcli has no equivalent).
Each skill loads only when its description matches the task, so the context cost per task stays small.

| Layer | Skill | Purpose |
|---|---|---|
| Foundation | fusion-inventory | Find VMs, resolve `.vmx` paths, shared conventions |
| Granular | fusion-power | Start, stop, suspend, reset one VM |
| | fusion-snapshot | Take, list, revert, delete, clone snapshots |
| | fusion-resources | vCPU, memory, disk size |
| | fusion-network | NICs and host networks |
| | fusion-guest | Run commands, copy files, screenshot, keystrokes |
| | fusion-storage | Disks, ISOs, shared folders |
| | fusion-template | Create, clone, templates |
| Macro | fusion-fleet | One action across many VMs (dry run first) |
| | fusion-lab | Multi-skill workflows: build, template, patch, reset, teardown |
| | fusion-health | Capacity and guest health report |

Install: copy or symlink each folder under `skills/` into `~/.claude/skills/`. Scripts locate each other relatively, so keep the `fusion-*` folders side by side.
