# A collection of miscellaneous but helpful commands

---

### Direct Troubleshooting Tools
- `ping [ip_address]`: Sends ICMP echo requests to verify basic end-to-end Layer 3 connectivity to a destination.
- `traceroute [ip_address]`: Identifies every hop (router) along the path taken to reach a specific destination network.

---

### Interface & Layer 2 Verification
- `show ip interface brief`: Displays a quick summary of all IPv4 interfaces, their IP addresses, status, and protocol conditions.
- `show ipv6 interface brief`: Shows a concise summary of IPv6 interface addresses, link-local addresses, and status.
- `show vlan brief`: Lists all configured VLANs on a switch, their status, and the specific ports assigned to them.
- `show interfaces status`: Provides a high-level view of switch ports, including speed, duplex settings, and VLAN assignments.
- `show mac address-table`: Displays the Layer 2 MAC address table, showing which MAC address is learned on which physical port.
- `show interfaces [interface_id]`: Provides detailed statistics for a specific port, including hardware address, input/output errors, collisions, and bandwidth utilization.
- `show interfaces trunk`: Identifies active operational VLAN trunks, displaying the encapsulation type, status, and the list of allowed VLANs.
- `show spanning-tree`: Displays the Spanning Tree Protocol (STP) topology, including the root bridge ID, local bridge priority, port states (Forwarding/Blocking), and path costs.
- `show etherchannel summary`: Shows the status of port channels, indicating the operational state of the bundle and which physical interfaces are active participants.

---

### Routing & Layer 3 Monitoring
- `show ip route`: Displays the current IPv4 routing table, showing connected, static, and dynamically learned routes.
- `show ipv6 route`: Displays the active IPv6 routing table to verify next-hop destinations and routing protocols.
- `show ip arp`: Maps Layer 3 IPv4 addresses to Layer 2 MAC addresses for locally connected networks.
- `show ipv6 neighbors`: Shows the IPv6 neighbor cache, acting as the IPv6 equivalent to the ARP table.

---

### Network Discovery & Topology
- `show cdp neighbors`: Lists directly connected Cisco devices, showing their local interface, remote interface, and device ID.
- `show cdp neighbors detail`: Provides deep details about neighbors, including their IP addresses, IOS version, and device capabilities.
- `show lldp neighbors`: Performs the exact same function as CDP, but utilizes the vendor-neutral Link Layer Discovery Protocol.

---

### System & Configuration Management
- `show running-config`: Displays the configuration currently active in the device's volatile RAM memory.
- `show startup-config`: Displays the configuration saved in NVRAM that will load when the device reboots.
- `write memory (or copy running-config startup-config)`: Saves your current active configuration to the startup configuration.
- `show version`: Displays system hardware details, software IOS version, uptime, boot source, and configuration register.
- `show flash:`: Lists files stored in the internal flash memory, usually including the IOS image and crash logs.
