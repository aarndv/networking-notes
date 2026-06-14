# Basic Routing Configurations (Static & RIPv2)

This note covers two ways to route traffic between networks on Cisco IOS devices:
- **Static routing** (manual routes)
- **RIPv2** (dynamic distance-vector routing)

## 1. Sample Topology

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/14e9867b-bd47-4936-86e3-3ab56106db35" />

### Addressing Summary

| Router | Local LAN (Loopback 0) | Uplink Interface (to Neighbor) | IP Address |
| :--- | :--- | :--- | :--- |
| R1 | 192.168.1.1/24 | Serial 0/0/0 (to R2) | 10.1.12.1/30 |
|  |  | Serial 0/1/0 (to R3) | 10.1.13.1/30 |
| R2 | 192.168.2.1/24 | Serial 0/0/0 (to R1) | 10.1.12.2/30 |
|  |  | Serial 0/0/1 (to R3) | 10.1.23.2/30 |
| R3 | 192.168.3.1/24 | Serial 0/1/0 (to R1) | 10.1.13.3/30 |
|  |  | Serial 0/0/1 (to R2) | 10.1.23.3/30 |

---

## 2. Static Routing

Static routes are configured by hand.

> Analogy: Static routes are like fixed directions printed on paper. They do not adapt automatically if a road changes.

### Syntax
```nasl
Router(config)# ip route <destination_network> <subnet_mask> <next_hop_ip | exit_interface> [distance]
```

### Example: R1
```nasl
R1# configure terminal
R1(config)# ip route 192.168.2.0 255.255.255.0 10.1.12.2
R1(config)# ip route 192.168.3.0 255.255.255.0 serial 0/1/0
R1(config)# exit
```

### Example: R2
```nasl
R2# configure terminal
R2(config)# ip route 192.168.1.0 255.255.255.0 10.1.12.1
R2(config)# ip route 192.168.3.0 255.255.255.0 10.1.23.3
R2(config)# exit
```

---

## 3. RIPv2

RIPv2 is a classic distance-vector routing protocol for small labs/networks.

- **Metric:** Hop count (max 15; 16 is unreachable)
- **Administrative Distance:** 120 (less preferred than OSPF AD 110 and static AD 1)
- **Update timer:** Every 30 seconds
- **Supports:** CIDR and VLSM (with `version 2` and typically `no auto-summary`)

### Example: R1
```nasl
R1# configure terminal
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# no auto-summary
R1(config-router)# network 10.1.12.0
R1(config-router)# network 10.1.13.0
R1(config-router)# network 192.168.1.0
R1(config-router)# passive-interface Loopback0
R1(config-router)# exit
```

### Example: R2
```nasl
R2# configure terminal
R2(config)# router rip
R2(config-router)# version 2
R2(config-router)# no auto-summary
R2(config-router)# network 10.1.12.0
R2(config-router)# network 10.1.23.0
R2(config-router)# network 192.168.2.0
R2(config-router)# passive-interface Loopback0
R2(config-router)# exit
```

> `default-information originate` is optional and should be used only when that router already has a valid default route you want to advertise.

---

## 4. Verification Commands

```nasl
Router# show ip route
Router# show ip protocols
Router# show ip rip database
```

Look for:
- `C` connected routes
- `S` static routes
- `R` RIP-learned routes
