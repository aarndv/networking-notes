# Configuring DHCP on a Layer 3 Switch

A Layer 3 (multilayer) switch can be configured to act as a centralized DHCP server. This eliminates the need for dedicated server hardware in small-to-medium enterprise networks, allowing the switch to handle both inter-VLAN routing via Switched Virtual Interfaces (SVIs) and dynamic IP address assignment simultaneously.

## 1. Network Topology Context

In this scenario, a single Layer 3 switch acts as the default gateway for multiple VLANs and provides IP configuration details (IP address, subnet mask, default gateway, and DNS server) to connected hosts.

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/24388491-fdef-4cd7-b166-06765edde19f" />

## 2. Step-by-Step Configuration Flow

### Step 1: Enable Layer 3 Routing

Multilayer switches must have IP routing enabled to properly route traffic between the DHCP pools.

```nasl
SW# configure terminal
SW(config)# ip routing
```

### Step 2: Configure the VLANs and SVIs

Create the underlying broadcast domains and configure the SVIs. The SVI IP address will serve as the default gateway for the devices in that pool.

```nasl
SW(config)# vlan 10
SW(config-vlan)# name Engineering
SW(config-vlan)# vlan 20
SW(config-vlan)# name Marketing
SW(config-vlan)# exit

SW(config)# interface vlan 10
SW(config-if)# ip address 192.168.10.1 255.255.255.0
SW(config-if)# no shutdown

SW(config)# interface vlan 20
SW(config-if)# ip address 192.168.20.1 255.255.255.0
SW(config-if)# no shutdown
SW(config-if)# exit
```

### Step 3: Configure DHCP Excluded Addresses

Always exclude static IP addresses—such as the default gateway (the SVI IP), printer IPs, or server IPs—before creating the DHCP pool. This prevents the switch from assigning duplicate addresses and causing IP conflicts.

```nasl
! Exclude a single IP address (the default gateway)
SW(config)# ip dhcp excluded-address 192.168.10.1
SW(config)# ip dhcp excluded-address 192.168.20.1

! Exclude a range of IP addresses (e.g., .1 through .10 for static infrastructure)
SW(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
SW(config)# ip dhcp excluded-address 192.168.20.1 192.168.20.10
```

### Step 4: Configure the DHCP Pools

Define the network range and the operational parameters passed to the clients when they request an address.

```nasl
! Configure DHCP Pool for VLAN 10
SW(config)# ip dhcp pool VLAN10_ENG
SW(dhcp-config)# network 192.168.10.0 255.255.255.0
SW(dhcp-config)# default-router 192.168.10.1
SW(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
SW(dhcp-config)# domain-name engineering.local
SW(dhcp-config)# exit

! Configure DHCP Pool for VLAN 20
SW(config)# ip dhcp pool VLAN20_MKT
SW(dhcp-config)# network 192.168.20.0 255.255.255.0
SW(dhcp-config)# default-router 192.168.20.1
SW(dhcp-config)# dns-server 1.1.1.1
SW(dhcp-config)# domain-name marketing.local
SW(dhcp-config)# exit
```

## 3. Configuration Alternative: The DHCP Relay Agent (ip helper-address)

If the enterprise relies on a dedicated, external centralized Windows or Linux DHCP server located in a separate management subnet (e.g., `10.50.1.100`), the Layer 3 switch must be configured to relay client requests.

Because client DHCP DISCOVER messages are Layer 2 broadcasts, they cannot pass through router boundaries or SVIs by default. The ip helper-address command intercepts these broadcasts on the SVI level, converts them to unicast traffic, and routes them straight to the designated server.

```nasl
SW(config)# interface vlan 10
! Intercepts broadcast DHCP requests and forwards them as unicast to the external server
SW(config-if)# ip helper-address 10.50.1.100
SW(config-if)# exit

SW(config)# interface vlan 20
SW(config-if)# ip helper-address 10.50.1.100
SW(config-if)# exit
```

## 4. Verification and Operational Diagnostics

Run these commands from privileged EXEC mode (#) to monitor pool utilization and resolve lease allocation failures.

### View Active Leases

Displays the MAC address-to-IP bindings currently granted to network clients, along with lease expiration details.

```nasl
SW# show ip dhcp binding
```

### Monitor Pool Statistics

Provides an administrative view of available addresses, active leases, and memory consumption metrics across all configured pools.

```nasl
SW# show ip dhcp server statistics
```
