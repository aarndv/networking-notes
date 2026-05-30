# MAC and IPv4 Addressing Fundamentals

## 1. Media Access Control (MAC) Addressing (Layer 2)

A MAC address is a permanent physical address burned into a network interface card (NIC) during manufacturing. It operates at Data Link Layer (Layer 2) of the OSI model to ensure hop-to-hop data delivery across the same local segment.

### Hardware Address Properties
- Bit Length: 48 bits, written as 12 hexadecimal digits (0−9, A−F).
- Format Structure: Typically written as `MM:MM:MM:SS:SS:SS`, `MM-MM-MM-SS-SS-SS`, or `MMMM.MMMM.MMMM`.
- The 24/24 Bit Split:
  - OUI (Organizationally Unique Identifier): The initial 24 bits (first 6 hex digits) designate the hardware manufacturer. Managed and assigned by the IEEE.
  - UAA (Universally Administered Address): The remaining 24 bits (last 6 hex digits) are a unique serial identifier assigned by the manufacturer.

### Frame Transmission Methods
- **Unicast:** Sent from a single source host to a single specific destination device. The first hextet's least significant bit is set to 0.
- **Multicast:** Sent to a selected group of subscription devices (e.g., routing updates). The first hextet's least significant bit is set to 1 (e.g., Ethernet addresses starting with 01:00:5E).
- **Broadcast:** Sent to every node on the local Layer 2 broadcast domain. Set entirely to binary 1s, represented in hex as FF:FF:FF:FF:FF:FF.

2. Internet Protocol Version 4 (IPv4) Addressing (Layer 3)

An IPv4 address is a logical identifier configured on an interface to facilitate end-to-end network routing across different geographic boundaries. It operates at Network Layer (Layer 3).

### Address Architecture
- Bit Length: 32 bits, written in a human-readable dotted-decimal format consisting of four 8-bit sections called octets separated by periods.
- Octet Values: Each octet ranges from 0 to 255 in decimal (00000000 to 11111112 in binary).

### The Two-Part Hierarchy

Every IPv4 address is split into two sections determined by its accompanying subnet mask:
- Network Portion: Identifies the specific logical network segment the host belongs to. Routers evaluate only this portion when forwarding packets.
- Host Portion: Identifies the specific physical device interface on that local network segment.

## 3. Subnet Masks and Prefix Logic

A subnet mask is a 32-bit sequence of consecutive binary 1s followed by consecutive binary 0s. It tells network hardware where the network portion ends and the host portion begins.

### Evaluating Data via ANDing

Devices perform a bitwise logical `AND` operation between their local IP address and their subnet mask to derive their Network ID:
- 1 AND 1=1
- 1 AND 0=0
- 0 AND 0=0

### Slash/CIDR Notation Comparison

Instead of writing full decimal masks, networks use Classless Inter-Domain Routing (CIDR) notation to count the total number of masking binary 1s:

| Subnet Mask (Decimal) | Subnet Mask (Binary Representation) | CIDR Prefix |
| :--- | :--- | :--- |
| `255.0.0.0` |	`11111111.00000000.00000000.00000000` |	/8 |
| `255.255.0.0` |	`11111111.11111111.00000000.00000000` |	/16 |
| `255.255.255.0`	| `11111111.11111111.11111111.00000000` |	/24 | 
| `255.255.255.192`	| `11111111.11111111.11111111.11000000` |	/26 | 

## 4. Special Address Roles in a Subnet

Within any given subnet block, two addresses are completely unusable for host assignment:
- Network Address: The very first address in the block, where all host bits are set to binary 0. It defines the entire subnet boundary line.
- Broadcast Address: The very last address in the block, where all host bits are set to binary 1. Packets sent here are replicated to every active device in that subnet.
- Host Range: The usable IP addresses falling between the Network Address and Broadcast Address.
- Formula for Usable Hosts: To calculate the maximum usable host addresses in any subnet where h represents the number of binary 0s (host bits):
$$2h−2$$

## 5. Legacy Classful IP Architecture

Before classless networking was introduced, IPv4 was split into five rigid, unchangeable categories determined by the value of the very first octet:
- Class A (`1.0.0.0` to `126.255.255.255`): Designed for massive organizations. Uses a default /8 mask, providing up to 16,777,214 hosts per network.
- Class B (`128.0.0.0` to `191.255.255.255`): Designed for medium-to-large environments. Uses a default /16 mask, providing up to 65,534 hosts per network.
- Class C (`192.0.0.0` to `223.255.255.255`): Designed for small networks. Uses a default /24 mask, providing up to 254 hosts per network.
- Class D (`224.0.0.0` to `239.255.255.255`): Reserved entirely for Multicast application streams. No host ranges are allocated.
- Class E (`240.0.0.0` to `255.255.255.255`): Reserved for military, experimental research, and future deployment testing.

### Key Exceptions
- 127.0.0.0/8 Block: Completely omitted from public Class A use. Reserved for local host software loopback testing (e.g., 127.0.0.1).
- 0.0.0.0: Represents an unspecified source or the system's default local route choice.

## 6. Public vs. Private IPv4 Spaces (RFC 1918)

Public IPv4 addresses must be registered globally and are unique across the global Internet routing table. To conserve the limited pool of 32-bit addresses, RFC 1918 established distinct private ranges that are non-routable on the public web. They can be reused across different internal corporate enterprises.

### RFC 1918 Private Ranges
- Class A Range: `10.0.0.0` to `10.255.255.255` (`10.0.0.0/8`)
- Class B Range: `172.16.0.0` to `172.31.255.255` (`172.16.0.0/12`)
- Class C Range: `192.168.0.0` to `192.168.255.255` (`192.168.0.0/16`)

### Link-Local Automatic Allocation (APIPA)

If a client machine is configured for dynamic DHCP retrieval but cannot reach a functional DHCP server on its local network segment, the operating system drops back to an unrouted Automatic Private IP Addressing (APIPA) address within the range 169.254.0.0 to 169.254.255.255 (169.254.0.0/16).MAC and IPv4 Addressing Fundamentals
