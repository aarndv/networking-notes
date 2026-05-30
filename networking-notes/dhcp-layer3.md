# Configuring DHCP on a Layer 3 Switch

A Layer 3 (multilayer) switch can be configured to act as a centralized DHCP server. This eliminates the need for dedicated server hardware in small-to-medium enterprise networks, allowing the switch to handle both inter-VLAN routing via Switched Virtual Interfaces (SVIs) and dynamic IP address assignment simultaneously.

## 1. Network Topology Context

In this scenario, a single Layer 3 switch acts as the default gateway for multiple VLANs and provides IP configuration details (IP address, subnet mask, default gateway, and DNS server) to connected hosts.

                  +-----------------------------------+
                  |      LAYER 3 SWITCH (SVI)         |
                  |  DHCP Server Engine Enabled       |
                  |  Pool 10: 192.168.10.0/24         |
                  |  Pool 20: 192.168.20.0/24         |
                  +-----------------+-----------------+
                                    |
                           [802.1Q Trunk]
                                    |
                 +------------------+------------------+
                 |                                     |
                 v                                     v
       [VLAN 10 Broadcast]                   [VLAN 20 Broadcast]
     Host A: (Requests IP)                 Host B: (Requests IP)

## 2. Step-by-Step Configuration Flow
