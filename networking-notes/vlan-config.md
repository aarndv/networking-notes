# VLAN and Inter-VLAN Routing Configuration Guide

This guide provides a comprehensive overview of Virtual Local Area Networks (VLANs), operational mechanics, design paradigms, and explicit step-by-step implementation procedures for Cisco IOS devices.

---

## 1. Architectural Concepts & Terminologies

In a traditional flat network architecture, a single local area network operates as one large broadcast domain. When a device broadcasts a frame, every physical host on that Layer 2 segment receives and processes it, leading to scaling constraints and potential security exposure. 

VLANs solve this by decoupling logical network segmentation from physical infrastructure constraints.

### Core Definitions

* **VLAN (Virtual Local Area Network)**: A logical segmentation of a Layer 2 network that isolates broadcast domains within the same physical switch infrastructure. Traffic cannot pass between different VLANs without a Layer 3 routing mechanism.
* **Broadcast Domain**: A logical division of a computer network where all nodes can reach each other by broadcast at the data link layer. VLANs split a single physical broadcast domain into multiple isolated logical ones.
* **Access Port**: A switch port dedicated to a single VLAN. It drops incoming tagged frames and strips outgoing tags before forwarding to end-user devices like PCs, printers, or servers that do not interpret VLAN headers.
* **Trunk Port**: A point-to-point link between infrastructure devices (switch-to-switch or switch-to-router) that carries traffic for multiple VLANs simultaneously over a single physical connection.
* **802.1Q Encapsulation**: The standard IEEE protocol used to tag Ethernet frames by inserting a 4-byte (32-bit) field into the frame header. This tag includes a 12-bit VLAN Identifier (VLAN ID), allowing supporting hardware to recognize up to 4,094 distinct VLANs.
* **Native VLAN**: A specific VLAN assigned to an 802.1Q trunk port where all untagged traffic is directed. Both ends of a trunk link must share the same Native VLAN configuration to avoid traffic leaks or security risks.
* **SVI (Switched Virtual Interface)**: A logical Layer 3 interface configured inside a multilayer switch. By binding an IP address to an internal VLAN, the SVI acts as the default gateway for all physical hosts assigned to that specific broadcast domain.

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/0d2f652e-972f-466f-9409-ecc1ba904b70" />

---

## 2. Logical and Physical Network Topologies

Understanding inter-VLAN routing require distinguishing between how devices are physically connected versus how traffic logically traverses the network.

### Physical Topology
The physical layout typically follows a Star or Extended-Star topology. Multiple end devices terminate into an Access Layer switch via standard twisted-pair Ethernet cabling. These Access switches then link to a Core or Distribution Layer Multilayer switch via physical fiber-optic or high-speed copper trunk cables. Physically, all devices converge on a centralized switching backbone.

### Logical Topology
Logically, the network is partitioned into distinct IP subnets matching specific VLAN IDs. Even if two hosts plug into adjacent ports on the exact same physical switch, if they belong to different VLANs, they reside on completely separate logical networks.

---

When Host A (VLAN 10) transmits a packet to Host D (VLAN 20):
1. The packet cannot travel straight across the physical switch layer.
2. It must be forwarded upstream to its default gateway (the SVI or Router subinterface).
3. The Layer 3 engine inspects the IP header destination, checks its routing table, and rewrites the Layer 2 header.
4. The packet travels back down the infrastructure to the destination VLAN segment.

---

## 3. Global VLAN Initialization

Isolating broadcast domains requires initializing the VLAN within the switch database before configuring physical ports. If an interface is assigned to a non-existent VLAN, that interface remains inactive until the corresponding VLAN ID is created.

### Creating and naming a VLAN

```nasl
SW# configure terminal
SW(config)# vlan 10
SW(config-vlan)# name Engineering
SW(config-vlan)# vlan 20
SW(config-vlan)# name Marketing
SW(config-vlan)# exit
```

## 4. Edge Layer Interface Assignment

Physical edge ports linking to edge endpoints must be defined explicitly as access ports and bound to their designated VLAN IDs.

### Single Interface Configuration

```nasl
SW(config)# interface fastethernet 0/1
SW(config-if)# description Link to Engineering Desktop
SW(config-if)# switchport mode access
SW(config-if)# switchport access vlan 10
SW(config-if)# exit
```

Bulk Interface Range Configuration

To optimize administration when provisioning large hardware blocks, use the range modifier.

```nasl
SW(config)# int range fastethernet 0/2 - 12
SW(config-if)# description Engineering Pool Ports
SW(config-if)# switchport mode access
SW(config-if)# switchport access vlan 10
SW(config-if)# exit

SW(config)# int range fastethernet 0/13 - 24
SW(config-if)# description Marketing Pool Ports
SW(config-if)# switchport mode access
SW(config-if)# switchport access vlan 20
SW(config-if)# exit
```

## 5. Infrastructure Trunk Optimization

Trunk interfaces maintain logical network separation across physical switch boundaries. Restricting allowed trunk traffic reduces administrative overhead and minimizes broadcast replication across unnecessary links.

```nasl
SW(config)# int range gi0/1 - 2
SW(config-if)# description Core Trunk Uplink Links
SW(config-if)# switchport trunk encapsulation dot1q
SW(config-if)# switchport mode trunk

! Define an explicit Native VLAN for untagged background management traffic
SW(config-if)# switchport trunk native vlan 99

! Prune the trunk to permit only valid configured infrastructure VLANs
SW(config-if)# switchport trunk allowed vlan 10,20,99
SW(config-if)# exit
```

## 6. Multilayer Switch Inter-VLAN Routing (SVI)

Deploying a Layer 3 (Multilayer) Switch utilizes hardware ASIC chips for wire-speed inter-VLAN routing, eliminating the throughput bottlenecks caused by single-link Router-on-a-Stick attachments.

```nasl
SW(config)# ip routing
```

_Note: Cisco multilayer switches function strictly as Layer 2 devices by default until global IP routing is explicitly turned on._

### Switched Virtual Interface Configuration

```nasl
SW(config)# int vlan 10
SW(config-if)# description Default Gateway for Engineering
SW(config-if)# ip add 192.168.10.1 255.255.255.0
SW(config-if)# no shut

SW(config)# int vlan 20
SW(config-if)# description Default Gateway for Marketing
SW(config-if)# ip add 192.168.20.1 255.255.255.0
SW(config-if)# no shut
SW(config-if)# exit
```

### Physical Layer 3 Routed Port Configuration

If you need to connect the multilayer switch directly to a border firewall or edge Internet router, you can convert a standard physical port into a dedicated Layer 3 interface, stripping away its switching capabilities.

```nasl
SW(config)# int gi0/24
SW(config-if)# description Routed Uplink Connection to Perimeter Firewall
SW(config-if)# no switchport
SW(config-if)# ip address 10.0.0.2 255.255.255.252
SW(config-if)# exit
```

## 7. Topology Validation and Diagnostic Commands

Run these execution state commands from privileged EXEC mode (#) to verify configuration integrity and trace paths during structural network anomalies.

### VLAN Database Audit

Validates that logical VLAN numbers are active and accurately bound to physical edge access ports.

```nasl
SW# show vlan brief
```

### Trunk Operational Analysis

Verifies the operational state of inter-switch connections, confirming native VLAN matches, explicit allowed listings, and active pruning states.

```nasl
SW# show interfaces trunk
```

### Hardware Layer 2 Address Tracking

Displays the dynamically built Content Addressable Memory (CAM) table, verifying which physical endpoints are communicating out of specific interfaces and tracking their learned VLAN IDs.

```nasl
SW# show mac address-table
```

### Interface Protocol Check

Provides a rapid validation of the Layer 1 and Layer 2 status of both physical ports and configured virtual SVIs.

```nasl
SW# show ip interface brief
```

### Hardware Routing Engine Verification

Displays the active internal Layer 3 routing matrix to confirm the switch successfully registers and paths data between the configured SVIs.

```nasl
SW# show ip route
```

## 8. Dynamic VLAN Propagation via VTP (VLAN Trunking Protocol)

To eliminate the manual replication of the VLAN database across every individual switch in the infrastructure, utilize VLAN Trunking Protocol (VTP). By defining a common VTP domain name and password, changes made on a designated master switch automatically propagate to all other participating local switches over active 802.1Q trunk links.

### Operational Modes
* **Server**: The default mode. Permits full modification of the VLAN database (create, modify, delete). These changes are broadcast to the domain via configuration revision numbers.
* **Client**: Receives VTP advertisements and synchronizes its own local VLAN database to match the server. A client cannot create or modify VLANs locally.
* **Transparent**: Does not synchronize its database with the VTP server and runs an independent local VLAN configuration. However, it will forward VTP advertisements out its trunk ports to down-stream switches.

              +-----------------------+
              |  VTP SERVER (Core)    |
              |  Domain: NetAdmin     |
              |  VLAN 10, 20 Created  |
              +-----------+-----------+
                          |
                 [802.1Q Trunk Link]
                          |
                          v
              +-----------+-----------+
              |  VTP CLIENT (Access)  |
              |  Domain: NetAdmin     |
              |  (VLANs Sync Auto)    |
              +-----------------------+


### Step-by-Step VTP Domain Configuration

#### 1. Configuring the Core Switch (VTP Server)

Set the primary switch to manage the central VLAN database. 

```nasl
SW-Core# configure terminal
SW-Core(config)# vtp mode server
SW-Core(config)# vtp domain NetAdmin
SW-Core(config)# vtp password SecureVtpPass123
SW-Core(config)# vtp version 2
SW-Core(config)# exit
```

#### 2. Configuring Edge Switches (VTP Clients)

Configure downstream switches to receive database updates automatically. They must match the domain name, password, and version exactly.

```nasl
SW-Access1# configure terminal
SW-Access1(config)# vtp mode client
SW-Access1(config)# vtp domain NetAdmin
SW-Access1(config)# vtp password SecureVtpPass123
SW-Access1(config)# vtp version 2
SW-Access1(config)# exit
```

## 9. VTP Status and Synchronization Validation

Run these commands on both server and client units to ensure synchronization variables and revision numbers are matching correctly.

### Database Sync Verification

Checks the current VTP operating mode, the active domain name, and the "Configuration Revision" number. Every database modification increments this number; clients will only sync if the server's revision number is higher than their own.

```nasl
SW-Access1# show vtp status
```

### Password Verification

Validates that the MD5 digest and password strings match perfectly across the infrastructure, preventing synchronization dropouts.

```nasl
SW-Access1# show vtp password
```
