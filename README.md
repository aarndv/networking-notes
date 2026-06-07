# Computer Networking Reference Notes

This repository contains practical Cisco IOS notes, quick command references, and networking fundamentals.

These notes are written for learners, not just experts. If you are new, start with:
- `/tmp/workspace/aarndv/networking-notes/networking-notes/general-notes/how-to-study-these-notes.md`
- `/tmp/workspace/aarndv/networking-notes/networking-notes/general-notes/osi-layers.md`

---

## Repository Layout

```text
networking-notes/
├── config-notes/
│   ├── basic-device-config.md
│   ├── dhcp-layer3.md
│   ├── interface-config.md
│   ├── pass-config.md
│   ├── remote-access-telnet-ssh.md
│   ├── routing-configs.md
│   ├── save-config.md
│   └── vlan-config.md
├── config-scripts/
│   ├── adv-router-conf-pass
│   ├── basic-router-conf-pass
│   ├── ssh-conf-setup
│   └── telnet-conf-setup
├── general-notes/
│   ├── how-to-study-these-notes.md
│   ├── ipv6-structure.md
│   ├── mac-and-ipv4.md
│   └── osi-layers.md
├── VLSM-class-notes.md
└── misc-helpful-commands.md
```

---

## Quick Verification Commands

Run from privileged EXEC mode (`#`) unless noted otherwise:
- `show vlan brief`
- `show interfaces trunk`
- `show mac address-table`
- `show ip interface brief`
- `show ipv6 interface brief`
- `show ip route`
- `show ipv6 route`
- `show cdp neighbors`
- `show lldp neighbors` (if LLDP is enabled on the platform)
- `show users`
- `show ip ssh`
