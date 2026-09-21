---
name: fusion-network
description: Inspect and change VMware Fusion VM network adapters and host virtual networks (NAT, host-only, bridged, custom vmnet) with vmcli and vmrun. Use for switching a NIC's network, adding or removing a NIC, connecting or disconnecting a NIC, finding MAC or IP, or listing host networks.
---

# Fusion networking

Let `V` be the `.vmx` path.

| Goal | Command |
|---|---|
| Host networks | `vmrun listHostNetworks` (vmnet0 bridged, vmnet1 host-only, vmnet8 NAT) |
| NICs of a VM | `vmcli "$V" Ethernet query` (label, connectionType, MAC, status) |
| Guest IP | `vmrun getGuestIPAddress "$V"` |
| Change type | `vmcli "$V" Ethernet SetConnectionType ethernet0 <type>` |
| Custom network name | `vmcli "$V" Ethernet SetNetworkName ethernet0 <name>` |
| Add or remove NIC | `Ethernet SetPresent ethernet1 <true\|false>`, or `Ethernet Purge ethernet1` to delete it |
| Connect at boot | `Ethernet SetStartConnected ethernet0 <true\|false>` |
| Live connect toggle | `Ethernet ConnectionControl --help` (running VM) |
| MAC address | `Ethernet SetAddressType --help` |
| Port forwards | `vmrun listPortForwardings <vmnetN>` |

## Rules

- `SetConnectionType` values are not listed in `--help`. Read the current `connectionType` from `Ethernet query` (NAT reports `nat`) and confirm the spelling for others on a scratch VM before use.
- Host subnet definitions live in `/Library/Preferences/VMware Fusion/networking` (root-owned). Do not edit it. Changing subnets or DHCP needs the Fusion network editor and sudo.
- Bridged mode exposes the VM on the physical LAN. Ask before switching a VM to bridged.
- A changed NIC may need a guest DHCP renew (fusion-guest) to pick up the new address.
