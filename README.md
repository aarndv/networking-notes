# Computer Networking Reference Notes

A structured compilation of technical reference guides, Cisco IOS configuration scripts, and foundational network engineering principles. This repository serves as a core deployment playbook and academic study resource for Layer 2 switching, Layer 3 routing, and essential infrastructure services.

---

## ── Repository Architecture ──

The documentation is systematically structured into functional directories based on the operational scope of the notes:

```text
networking-notes/
├── config-notes/
│   ├── basic-device-config.md        # Initial device parameters, security banners, and line configurations
│   ├── dhcp-layer3.md                # Centralized DHCP pools, address exclusions, and relay agents
│   ├── interface-config.md           # Speed, duplex, description, and status management configurations
│   ├── pass-config.md                # Password encryption schemes and console/enable protection layers
│   ├── remote-access-telnet-ssh.md   # Plaintext Telnet vs. SSHv2 RSA key generation and VTY hardening
│   ├── routing-configs.md            # Static routes, next-hop validation, and dynamic RIPv2 propagation
│   ├── save-config.md                # Running-config vs. startup-config state management operations
│   └── vlan-config.md                # Access ports, 802.1Q trunking, SVIs, and native VLAN assignment
├── config-scripts/
│   ├── adv-router-conf-pass          # Advanced administrative configuration and credential scripting templates
│   └── basic-router-conf-pass        # Foundational device deployment and initialization scripts
├── general-notes/                    # Conceptual frameworks, architectural models, and theory notes
├── VLSM-class-notes.md               # Subnetting mechanics, variable-length subnet masks, and host math
├── misc-helpful-commands.md          # Troubleshooting toolkit (ping, traceroute, and interface database audits)
└── README.md                         # Repository deployment framework and administrative guide
```

## ── Primary Operational Verification Cheat Sheet ──

Execute these validation commands from privileged EXEC mode (#) to quickly audit network status during troubleshooting:
- VLAN Database Verification: `show vlan brief`
- Inter-Switch Trunk Operational Analysis: `show interfaces trunk`
- Layer 2 Hardware Address Tracking: `show mac address-table`
- Layer 3 Interface Verification Summary: `show ip interface brief` / `show ipv6 interface brief`
- Active Routing Matrix Evaluation: `show ip route / show ipv6 route`
- Infrastructure Discovery Protocol Matrix: `show cdp neighbors` / `show lldp neighbors`
- Active Security and Connection Tracking: `show users` / `show ip ssh`
