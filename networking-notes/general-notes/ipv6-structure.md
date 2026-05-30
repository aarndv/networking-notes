# IPv6 Address Structure and Configuration Notes

## 1. IPv6 Fundamentals and Notation
- **Bit Length**: IPv6 addresses are 128-bit logical expressions partitioned into eight 16-bit segments called hextets separated by colons.
- **Radix**: Written using case-insensitive hexadecimal notation (0−9, A−F). One hex digit equals 4 bits (1 nibble).
- **Address Space Size**: Supports 2128 or approximately 340 undecillion unique addresses.

## 2. Address Compression Rules

### Rule 1: Leading Zero Omission

Leading zeros within any given 16-bit hextet can be dropped. Trailing zeros must remain.
- Expanded: `3ffe:0404:0001:1000`
- Compressed: `3ffe:404:1:1000`

### Rule 2: Double Colon Substitution (::)

A single contiguous sequence of all-zero hextets can be replaced by a double colon. This operation can only be performed once per address to prevent ambiguity during expansion.
- Expanded: `ff02:0000:0000:0000:0000:0500`
- Compressed: `ff02::500`

If multiple non-contiguous all-zero blocks exist, compress the longest sequence and represent the remaining blocks with a single 0.
- Expanded: `fbb2:0000:0000:252b:0000:0000:f0ba`
- Valid Options: `fbb2::252b:0:0:f0ba` OR `fbb2:0:0:252b::f0ba`

## 3. IPv6 Prefix and Network Separation

IPv6 networks are split into a network portion and a host portion using Classless Inter-Domain Routing (CIDR) bitcount slash notation.
- Example: `3ffe:1944:100:a::/64`
- The `/64` prefix length marks the initial 64 bits as the routing network boundary, leaving the remaining 64 bits for the local host identifier.

## 4. IPv6 Address Types
- Unicast: One-to-one communication.
  - Global Unicast Address (GUA): Publicly routable Internet addresses. Globally scoped, starting within the range `2000::/3` through `3fff::/3`.
  - Link-Local Address (LLA): Non-routable local link addresses automatically created on all active IPv6 interfaces. Scoped within `fe80::/10` to `febf::/10`.
  - Loopback: Local host diagnostic interface. Represented as `::1/128`.
  - Unspecified Address: Indicates the absence of an address. Represented as `::/128`.
- Multicast: One-to-many communication. Delivered to all interfaces belonging to a designated multicast group.
- Anycast: One-to-nearest communication. Assigned to multiple physical nodes; routing protocols deliver the packet to the topologically closest interface.
_Note: IPv6 lacks an explicit broadcast address type; it uses specialized link-local multicast groups instead._

## 5. Architectural Topology of a Global Unicast Address

General Allocation Split
+----------------------------+-----------------------+---------------------------------+
| Global Routing Prefix (n)  |     Subnet ID (m)     |       Interface ID (128-n-m)    |
+----------------------------+-----------------------+---------------------------------+

Standard Enterprise /48 Allocation Block
+------------------------------------+--------------------------+---------------------------------+
|    Global Routing Prefix (/48)     |     Subnet ID (/16)      |       Interface ID (/64)        |
+------------------------------------+--------------------------+---------------------------------+

### The 3-1-4 Architecture Breakdown

A standard GUA can be analyzed by its structural hextet groupings:
- First 3 Hextets (Bits 0-48): Global Routing Prefix.
  - Bits 0-23: Regional Registry allocation identifier (Continent/Geographic zone).
  - Bits 24-32: Tier-1/Tier-2 Internet Service Provider (ISP) network prefix.
  - Bits 33-48: Customer Enterprise site identifier prefix.
- 4th Hextet (Bits 49-64): Dedicated 16-bit Subnet ID, providing up to 65,536 unique internal subnets for the enterprise network.
- Last 4 Hextets (Bits 65-128): Interface ID (64-bit host portion representing the specific device MAC or token address).

## 6. IPv6 Subnetting

When dividing a /48 allocation block into individual /64 networks, increment the value inside the 4th hextet (Subnet ID zone):
- Base Infrastructure Prefix: `2340:1111:AAAA::/48`
- Subnet 1: `2340:1111:AAAA:1::/64`
- Subnet 2: `2340:1111:AAAA:2::/64`
- Subnet 3: `2340:1111:AAAA:3::/64`

## 7. IOS Interface Configuration and Autoconfiguration

To configure an interface with an IPv6 address and enable dynamic stateless address distribution to client hosts, run the following commands:

```nasl
Router# configure terminal
! CRITICAL: Enables global IPv6 packet forwarding and Router Advertisements (RA)
Router(config)# ipv6 unicast-routing

Router(config)# interface gigabitethernet 0/0/0
Router(config-if)# description Link to Internal LAN Segment
Router(config-if)# ipv6 address 2001:db8:caff:2::1/64
Router(config-if)# no shutdown
Router(config-if)# exit
```

### SLAAC/Dynamic Host Allocation

Once `ipv6 unicast-routing` is active globally and an explicit global unicast address is assigned to the physical interface, the router begins transmitting periodic ICMPv6 Router Advertisement (RA) packets down that link segment.

Connected client operating systems set to "Automatic IP Configuration" intercept these RA packets and automatically configure their own IPv6 addresses using SLAAC without requiring a dedicated stateful DHCPv6 deployment.
