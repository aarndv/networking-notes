# Remote Access Configuration: Telnet vs. Secure Shell (SSH)

This note covers the configuration, operation, and security implications of virtual terminal (VTY) lines for remote device management. It details the setup of legacy unencrypted Telnet, the migration to cryptographic Secure Shell (SSH), local database authentication, and line hardening procedures.

## 1. Network Management Topology

For remote access configurations, a management workstation establishes a virtual session across the network to an in-band IP address configured on a target device (e.g., an SVI on a switch or a physical interface on a router).

## 2. Legacy Telnet Configuration & Vulnerabilities

Telnet operates over TCP port 23. It is a legacy protocol that transmits all traffic—including administrative usernames and plaintext passwords—in cleartext. Anyone with a packet sniffer (like Wireshark) on the data path can intercept these credentials. It should only be used in isolated laboratory environments.

### Step 1: Configuring an IP Address for Management 

The device must have Layer 3 connectivity before accepting remote terminal sessions.

```nasl
Router# configure terminal
Router(config)# int gigabitethernet 0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
```

### Step 2: Enabling Basic Telnet Remote Access

By default, Cisco VTY lines reject connections if an access password is not explicitly defined.

```nasl
Router(config)# line vty 0 4
Router(config-line)# password TelnetPassword123
Router(config-line)# login
Router(config-line)# transport input telnet
Router(config-line)# exit
```

### Step 3: Accessing the Device via Telnet

From a remote administration workstation command line, connection is initiated via the target IP address:

```bash
C:\> telnet 192.168.1.1
```

_The user will immediately be prompted for the line password (TelnetPassword123) to access User EXEC mode._

## 3. Alternative: Telnet with Local Database Authentication

Instead of using a single shared line password for Telnet, you can enforce individual accountability by authenticating users against the device's local database. This requires an administrative username and password to be configured first.

### Step 1. Create a Local User Account

```nasl
Router(config)# username admin privilege 15 secret LocalTelnetPass555
```

### Step 2. Enforce Local Authentication on the VTY Lines

```nasl
Router(config)# line vty 0 4

! Tells the device to look at the local username database instead of a line password
Router(config-line)# login local
Router(config-line)# transport input telnet
Router(config-line)# exit
```

### Step 3. Accessing the Device

When connecting via Telnet, the device will now explicitly prompt for both a username and a password rather than just a password:

```bash
C:\> telnet 192.168.1.1
User Access Verification

Username: admin
Password: 
Router#
```
_Security Note: While using login local with Telnet provides better administrative accountability than a single line password, the entire session—including the username and password entered here—is still sent over the network in cleartext and can be easily captured by packet sniffers. It should still be replaced by SSH._

## 4. Hardening the Device: Migrating to Secure Shell (SSH)

SSH operates over TCP port 22. It encrypts all communications, protecting passwords and configuration commands from packet sniffing. Implementing SSH requires local database credentials and an RSA cryptographic key pair.

### Step 1: Set Hostname and Domain Name

Cisco IOS uses the hostname and domain name together as a fully qualified domain name (FQDN) to seed the cryptographic key generation algorithm.

```nasl
Router(config)# hostname NetCore
NetCore(config)# ip domain-name enterprise.local
```

### Step 2: Generate the RSA Cryptographic Key Pair

Generate the public and private key pairs used to encrypt the SSH traffic.

```nasl
NetCore(config)# crypto key generate rsa general-keys modulus 1024
```

### Step 3: Enforce SSH Version 2

Disable the vulnerable, legacy SSH Version 1 protocol.

```nasl
NetCore(config)# ip ssh version 2
```

### Step 4: Define Local Administrator Accounts

Instead of a single shared line password, create unique local user credentials to provide accountability and support role-based privileges.

```nasl
! Creates an admin account with full root administrative privileges (Privilege 15)
NetCore(config)# username admin privilege 15 secret StrongAdminPass987
```

## 5. Locking Down the VTY Lines

Once SSH parameters and local users are defined, the virtual lines must be explicitly reconfigured to force authentication against the local database and block all unencrypted inbound management methods.

```nasl
NetCore(config)# line vty 0 4
! Instructs the lines to validate credentials against the local username/secret database
NetCore(config-line)# login local

! CRITICAL: Explicitly restrict the input protocol to SSH only, completely disabling Telnet
NetCore(config-line)# transport input ssh

! Optional performance hardening parameters
NetCore(config-line)# exec-timeout 10 0
NetCore(config-line)# exit
```

## 6. Connecting and Verifying the Remote Session

### Connecting via SSH Client

From an external workstation deployment using a command-line utility or Terminal emulator:

```bash
ssh -l admin 192.168.1.1
```

_Alternatively:_

```bash
ssh admin@192.168.1.1
```

### Verification and Operational Diagnostics

Validates that the SSH server engine is active, displays the operational version, and shows administrative timeout metrics.

```nasl
NetCore# show ip ssh
```

### Track Established Remote Sessions

Displays real-time details regarding active terminal lines, connection states, usernames logged in, and the origin IP addresses of the management computers.

```nasl
NetCore# show users
```
