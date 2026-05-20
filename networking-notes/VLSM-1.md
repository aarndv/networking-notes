# Comprehensive Network Engineering Reference Guide

---

## 1. Variable Length Subnet Masking (VLSM)

Variable Length Subnet Masking (VLSM) allows the allocation of subnets with varying sizes based on host requirements, preventing the address waste inherent in classful addressing architectures. 

To determine the subnet mask for a specific host requirement, use the following calculation:

$$2^h - 2 \ge \text{Required Hosts}$$

Where $h$ represents the number of host bits remaining in the identifier.

### Subnet Allocation Table
*   **Base Network:** 10.10.0.0/16
*   **Base Subnet Mask:** 255.255.0.0

| Order | Required Hosts | Allocated Subnet | First Usable IP | Last Usable IP | Broadcast IP | Subnet Mask | CIDR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1st** | 500 | 10.10.0.0 | 10.10.0.1 | 10.10.1.254 | 10.10.1.255 | 255.255.254.0 | /23 |
| **2nd** | 75 | 10.10.2.0 | 10.10.2.1 | 10.10.2.126 | 10.10.2.127 | 255.255.255.128 | /25 |
| **3rd** | 50 | 10.10.2.128 | 10.10.2.129 | 10.10.2.190 | 10.10.2.191 | 255.255.255.192 | /26 |
| **4th** | 20 | 10.10.2.192 | 10.10.2.193 | 10.10.2.222 | 10.10.2.223 | 255.255.255.224 | /27 |
| **5th** | 2 | 10.10.2.224 | 10.10.2.225 | 10.10.2.226 | 10.10.2.227 | 255.255.255.252 | /30 |
| **6th** | 2 | 10.10.2.228 | 10.10.2.229 | 10.10.2.230 | 10.10.2.231 | 255.255.255.252 | /30 |

---

## 2. Global Initialization and Device Hardening Template

Before deploying configuration templates, purge residual data on test or staging devices to prevent structural conflicts.

```nasl
! Reset device to factory defaults
erase startup-config
reload
```

### Security Standardization Script
This baseline configuration enforces credential complexity, mitigation of brute-force validation attempts, administrative session timeouts, and visual legal compliance markers.

```nasl
! Global Security Settings
no ip domain-lookup
security passwords min-length 10
login block-for 180 attempts 4 within 120
service password-encryption

! Executive Control Configuration
enable secret midtermexam101

! Console Line Configuration
line con 0
 password midtermexam101
 login
 exec-timeout 5 0
 logging synchronous
exit

! Virtual Terminal Line Configuration
line vty 0 4
 password midtermexam101
 login
 exec-timeout 5 0
exit

! Legal Compliance Banner
banner motd @
#################################################################################################################################
							CYBERSECURITY WARNING

Unauthorized access, tampering, interception, or misuse of this device, system, or network is strictly prohibited. Violators may be subject to criminal prosecution and civil liabilities under applicable Philippine laws, including the Cybercrime Prevention Act of 2012, as well as the relevant international cyber sec and data protection regulations.

All activities may be monitored, logged and used as evidence in legal proceedings.
#################################################################################################################################
@
```

---

## 3. Secure Shell (SSHv2) Implementation

SSHv2 replaces insecure Telnet connections by encrypting all operational transit data.

### Step-by-Step Deployment
1. Verify system image capability for cryptographic implementations via `show ip ssh`.
2. Assign a fully qualified domain name to construct the encryption keys.
3. Generate the Rivest-Shamir-Adleman (RSA) key pair with a cryptographic strength of at least 1024 bits.
4. Establish local credential verification databases with explicit privilege layer mappings.
5. Restrict the virtual transport infrastructure to access requests initiated via SSH protocols exclusively.

```nasl
! Step 1: Verify Capability
show ip ssh

! Step 2 & 3: Domain and Key Provisioning
configure terminal
 ip domain-name Midterm.com
 crypto key generate rsa general-keys modulus 1024
 
! Note: To clear existing keys, use: crypto key zeroize rsa

! Step 4: Access Control and Local Database Population
 username Admin1 privilege 15 secret Midterm101

! Step 5: VTY Hardening and Protocol Enforcement
 line vty 0 4
  login local
  transport input ssh
  exec-timeout 5 0
 exit
 ip ssh version 2
```

### Remote Execution Syntax
```bash
ssh -l Admin1 <target_ip_address>
```

---

## 4. Interface Provisioning and Bidirectional Static Routing

### Point-to-Point Interface Configuration
```nasl
! Local Router Configuration
interface serial 0/2/0
 ip address 10.10.0.1 255.255.255.252
 no shutdown
exit

! Remote Peer Configuration (Core2)
hostname Core2
interface serial 0/2/0
 ip address 10.10.0.2 255.255.255.252
 no shutdown
exit
```

### Static Route Implementation
> **Critical Infrastructure Correction:** Network routing requires symmetrical configuration. If Router A contains a static route to reach a destination prefix via Router B, Router B must possess an explicit return route to target prefixes managed by Router A. Omitting bidirectional entries results in asymmetrical routing execution failures and systemic data drops.

```nasl
! Verifying the routing infrastructure table
show ip route

! Injecting a static path targeting the /23 network boundary
! Syntax: ip route [destination-network] [subnet-mask] [next-hop-ip]
ip route 10.0.16.0 255.255.254.0 10.10.0.1
```

### Local Host Resolution Table
Local mapping prevents repetitive domain lookup failures and speeds up node-to-node reachability assessments.

```nasl
! Configure on FEURouter
ip host Core2 10.10.0.2
ip host FEURouter 10.10.0.1

! Configure on Core2
ip host FEURouter 10.10.0.1
ip host Core2 10.10.0.2
```

---

## 5. Application Layer Services

### DHCP Server Deployment
When implementing Dynamic Host Configuration Protocol services via standard application interfaces, complete the following parameters:
*   Toggle the service status to **ON**.
*   Specify the default gateway address matching the local routing interface.
*   Define the origin boundary of the dynamic address allocation pool alongside its respective subnet mask.
*   Enforce a maximum limit for concurrently active leases to optimize available space.

### HTTP and DNS Server Provisioning
To configure standard web name resolution services inside simulated laboratory architectures, execute the workflow below:

1. Locate the **HTTP** system configuration module and modify the `index.html` structure to meet institutional layout guidelines.
2. Navigate to the **DNS** operational interface and enable functionality.
3. Map an **A Record** correlating the canonical resource domain format (e.g., `www.feutech.edu.ph`) directly to the web server's static IP address (e.g., `10.10.2.10`).
4. Implement a Canonical Name (**CNAME**) mapping if alternative domain aliases must resolve to the primary host record. The authoritative **A Record** must be present in the server's database before configuring dependent CNAME entries.
5. Trigger a dynamic client lease renewal via terminal execution to populate modified DNS entries across edge network endpoints.

```bash
! Target endpoint client commands for lease validation
ipconfig /release
ipconfig /renew
```

## 6. WAN Edge and Service Provider Connectivity

To establish connectivity between the enterprise edge core network and the Internet Service Provider (ISP), define an authoritative default static route targeting the next-hop IP address of the ISP gateway.

```nasl
! Verify directly connected neighbors via Cisco Discovery Protocol (CDP)
show ip cdp neighbors

! Configure a default static route pointing to the ISP edge boundary
configure terminal
 ip route 0.0.0.0 0.0.0.0 202.202.202.194
exit
```

---

## 7. Inter-VLAN Routing Methodologies

Managing traffic between isolated broadcast domains (VLANs) can be achieved through legacy subinterface routing or high-performance multilayer switching.

### Methodology A: Router-on-a-Stick (RoaS)
This legacy implementation utilizes a single physical interface partitioned into multiple logical subinterfaces. Each subinterface acts as a default gateway for its assigned VLAN and requires explicit 802.1Q encapsulation tagging.

```nasl
! Provisioning subinterfaces for Faculty (VLAN 300) and IT Support (VLAN 600)
configure terminal
 interface gigabitEthernet 0/0/0.300
  encapsulation dot1q 300
  ip address 10.0.18.1 255.255.254.0
 exit
 
 interface gigabitEthernet 0/0/0.600
  encapsulation dot1q 600
  ip address 10.0.24.1 255.255.255.0
 exit
```

#### Teardown and Deconfiguration Commands
```nasl
! Remove logical subinterfaces from the configuration register
configure terminal
 no interface gigabitEthernet 0/0/0.600
 no interface gigabitEthernet 0/0/0.300
exit
```

### Methodology B: Multilayer (Layer 3) Core Switch Deployment
Deploying a Layer 3 switch is preferred over Router-on-a-Stick for corporate networks. RoaS creates a single-link performance bottleneck because all inter-VLAN traffic must cross the same physical link. A Layer 3 switch processes routing decisions locally in specialized hardware (ASICs) at wire-speed, drastically lowering latency and increasing throughput.

#### Step 1: Interface De-activation of Layer 2 Attributes (Routed Port)
To link the edge router directly to the Layer 3 core switch, convert a standard Layer 2 switchport into a routed port.

```nasl
! Configure the local FEUDiliman Edge Router
configure terminal
 interface serial 0/1/0
  ip address 10.10.0.1 255.255.255.252
  no shutdown
 exit
```

```nasl
! Configure the SWCore3 Multilayer Switch
configure terminal
 hostname SWCore3
 interface gigabitEthernet 0/2
  no switchport
  ip address 10.10.0.2 255.255.255.252
  no shutdown
 exit
 
! Verify end-to-end layer 3 reachability to the router boundary
do ping 10.10.0.1
```

---

## 8. VLAN Trunking Protocol (VTP) and Trunk Infrastructure

VTP automates the creation, deletion, and synchronization of VLAN databases across an entire switched network domain, minimizing administrative overhead.

```nasl
! Verify baseline database status and operational modes
show vtp status
```

### Multi-Layer Switch (VTP Server) Configuration
```nasl
configure terminal
 vlan 300
  name Faculty
 vlan 600
  name ITSupport
 exit
 
 ! Establish the VTP synchronization administrative framework
 vtp domain Tamaraw
 vtp password tamaraw
 vtp mode server
```

### Layer 2 Switch (VTP Client) Configuration
```nasl
configure terminal
 hostname SWDiliman
 vtp domain Tamaraw
 vtp password tamaraw
 vtp mode client
```

### Inter-Switch Trunk Link Provisioning
Trunk ports carry traffic from multiple VLANs over a single physical link between switches, whereas access ports carry traffic for only one specific VLAN (typically to end devices like PCs).

```nasl
! Step 1: Enforce encapsulation protocol constraints on the Core Switch
SWCore3(config)# interface gigabitEthernet 0/1
SWCore3(config-if)# switchport trunk encapsulation dot1q
SWCore3(config-if)# switchport mode trunk
```

```nasl
! Step 2: Configure the peer link on the Access Switch
SWDiliman(config)# interface gigabitEthernet 0/1
SWDiliman(config-if)# switchport mode trunk
```

---

## 9. Layer 2 Access Layer Configuration

Map edge ports on the access switch to their corresponding VLANs based on department deployment rules.

```nasl
configure terminal
 ! Map port range to the Faculty broadcast domain
 interface range fastEthernet 0/1-14
  switchport mode access
  switchport access vlan 300
 exit
 
 ! Map port range to the IT Support broadcast domain
 interface range fastEthernet 0/15-24
  switchport mode access
  switchport access vlan 600
 exit
```

### End-Node Network Interface Card (NIC) Profiling
To verify network connectivity, configure a workstation in the IT Support domain with these static parameters:
*   **IP Address:** 10.0.24.50
*   **Subnet Mask:** 255.255.255.0
*   **Default Gateway:** 10.0.24.1
*   **Primary DNS Server:** 10.0.16.10

---

## 10. Core Switch Routing and Gateway Validation

Define Switched Virtual Interfaces (SVIs) on the multilayer switch to provide default gateways for local hosts, and enable the global hardware routing engine.

```nasl
configure terminal
 ! Provision the gateway address for VLAN 300
 interface vlan 300
  ip address 10.0.18.1 255.255.254.0
  no shutdown
 exit
 
 ! Provision the gateway address for VLAN 600
 interface vlan 600
  ip address 10.0.24.1 255.255.255.0
  no shutdown
 exit

 ! Global execution instruction to activate internal Layer 3 lookup tables
 ip routing
exit
```
