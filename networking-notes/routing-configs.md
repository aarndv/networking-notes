# Basic Routing Configurations (Static & RIPv2)

### This note covers fundamental methods for enabling Layer 3 communication between disparate networks using Cisco IOS routers. It details both administrative manually configured paths (Static) and dynamic, protocol-driven route propagation (RIPv2).

## 1. Sample Routing Topology

To follow the configurations throughout this note, we will use a standardized multi-router topology.

### Addressing Scheme Summary

| Router | Local LAN (Loopback 0)	| Uplink Interface (to Neighbor) | IP Address |
| :--- | :--- | :--- | :--- |
| R1 | 192.168.1.1/24	| Serial 0/0/0 (to R2) | 10.1.12.1/30 |
| | | Serial 0/1/0 (to R3) | 10.1.13.1/30 |
| R2 | 192.168.2.1/24	| Serial 0/0/0 (to R1) | 10.1.12.2/30 |
| | | Serial 0/0/1 (to R3) | 10.1.23.2/30 |
| R3 | 192.168.3.1/24 | Serial 0/1/0 (to R1) | 10.1.13.3/30 |
| | | Serial 0/0/1 (to R2) | 10.1.23.3/30 |
