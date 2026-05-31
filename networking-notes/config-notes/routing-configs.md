# Basic Routing Configurations (Static & RIPv2)

### This note covers fundamental methods for enabling Layer 3 communication between disparate networks using Cisco IOS routers. It details both administrative manually configured paths (Static) and dynamic, protocol-driven route propagation (RIPv2).

## 1. Sample Routing Topology

To follow the configurations throughout this note, we will use a standardized multi-router topology.

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/14e9867b-bd47-4936-86e3-3ab56106db35" />

### Addressing Scheme Summary

| Router | Local LAN (Loopback 0)	| Uplink Interface (to Neighbor) | IP Address |
| :--- | :--- | :--- | :--- |
| R1 | 192.168.1.1/24	| Serial 0/0/0 (to R2) | 10.1.12.1/30 |
| | | Serial 0/1/0 (to R3) | 10.1.13.1/30 |
| R2 | 192.168.2.1/24	| Serial 0/0/0 (to R1) | 10.1.12.2/30 |
| | | Serial 0/0/1 (to R3) | 10.1.23.2/30 |
| R3 | 192.168.3.1/24 | Serial 0/1/0 (to R1) | 10.1.13.3/30 |
| | | Serial 0/0/1 (to R2) | 10.1.23.3/30 |

## 2. Static Routing

Static routing is manually configured by the administrator. It defines precise paths to destination networks. It is simple, secure, and has no protocol overhead (it uses no bandwidth or CPU for updates).

Command Syntax
```nasl
Router(config)# ip route <destination_network> <subnet_mask> <next_hop_ip | exit_interface> [distance]
```

### Static Route Configuration (Sample Network)

Our goal is full connectivity using only static paths. Each router needs a route to every remote subnet.

### Configuring R1 (West Router)

R1 is missing routes to R2's LAN and R3's LAN.

```nasl
R1# configure terminal
! Route to R2s LAN via next-hop R2 Se0/0/0
R1(config)# ip route 192.168.2.0 255.255.255.0 10.1.12.2

! Route to R3s LAN via exit interface Se0/1/0 (useful when next-hop IP is unknown)
R1(config)# ip route 192.168.3.0 255.255.255.0 serial 0/1/0
R1(config)# exit
```

### Configuring R2 (East Router)

R2 is missing routes to R1's LAN and R3's LAN.

```nasl
R2# configure terminal
! Route to R1s LAN via next-hop R1 Se0/0/0
R2(config)# ip route 192.168.1.0 255.255.255.0 10.1.12.1

! Route to R3s LAN via next-hop R3 Se0/0/1
R2(config)# ip route 192.168.3.0 255.255.255.0 10.1.23.3
R2(config)# exit
```

## 3. RIPv2 (Routing Information Protocol version 2)

RIP is a distance-vector dynamic routing protocol. It is suitable for small, homogeneous networks.
- Metric: Hop Count (Maximum 15 hops; 16 is unreachable).
- Administrative Distance: 120 (Less preferred than static, more preferred than OSPF).
- Updates: Every 30 seconds (entire database is sent; slow convergence).
- Version 2 Features: Supports Classless Inter-Domain Routing (CIDR), Variable Length Subnet Masking (VLSM), and authentication.

### Basic RIPv2 Configuration

RIPv2 activation involves three steps: entering the router sub-mode, specifying the version, and advertising the major network addresses that the router participates in.

### Configuring R1 (West Router)

R1 participates in networks 192.168.1.0, 10.1.12.0, and 10.1.13.0.

```nasl
R1# configure terminal
R1(config)# router rip
R1(config-router)# version 2
! Disable auto-summarization at major network boundaries (essential for VLSM)
R1(config-router)# no auto-summary
! Advertise the interfaces belonging to 10.1.12.0 major network
R1(config-router)# network 10.1.12.0
! Advertise the interfaces belonging to 10.1.13.0 major network
R1(config-router)# network 10.1.13.0
! Advertise the interfaces belonging to 192.168.1.0 major network
R1(config-router)# network 192.168.1.0
! Stop sending unnecessary RIP updates out of the local LAN interface (Secures the LAN)
R1(config-router)# passive-interface Loopback0
R1(config-router)# exit
```

### Configuring R2 (East Router)

R2 participates in networks 192.168.2.0, 10.1.12.0, and 10.1.23.0.

```nasl
R2# configure terminal
R2(config)# router rip
R2(config-router)# version 2
R2(config-router)# no auto-summary

! Inject the default static route into all outbound RIP updates automatically.
! Used on the edge router that connects an internal enterprise network to the broader internet.
Router(config-router)# default-information originate

! Prevent routing updates from being broadcasted out host-facing interfaces
Router(config-router)# passive-interface fastethernet 0/1

R2(config-router)# network 10.1.12.0
R2(config-router)# network 10.1.23.0
R2(config-router)# network 192.168.2.0

! Optional
R2(config-router)# passive-interface Loopback0
R2(config-router)# exit
```

## 4. Verification and Troubleshooting

Use these commands to validate the routing configuration and isolate issues.

### Routing Table Verification

The most essential command for all routing validation.

```nasl
Router# show ip route
```

Look for:
- `C`: Directly connected interfaces (Should show all network commands).
- `S`: Static routes (Must show S [1/0] ... via next-hop).
- `R`: RIPv2 learned routes (Must show R [120/1] ... via next-hop).

### Protocol Specifics (RIP)

Checks the protocol's operating health, update intervals, and neighboring routers.

```nasl
Router# show ip protocols
```

### RIP Database

Shows all networks learned via RIPv2, before the best path is chosen for the main routing table.

```nasl
Router# show ip rip database
```
 
