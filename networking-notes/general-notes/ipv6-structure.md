# IPv6 Address Structure and Configuration Notes

## 1. IPv6 Basics
- **Length:** 128 bits.
- **Format:** Eight 16-bit blocks (hextets), separated by colons.
- **Digits:** Hexadecimal (`0-9`, `a-f`, case-insensitive).
- **Address space:** \(2^{128}\) total addresses.

---

## 2. Compression Rules

### Rule 1: Remove Leading Zeros
Inside each hextet, leading zeros can be omitted.
- Expanded: `3ffe:0404:0001:1000:0000:0000:0000:0001`
- Compressed: `3ffe:404:1:1000:0:0:0:1`

### Rule 2: Use `::` Once for Longest Zero Run
A contiguous run of all-zero hextets can be replaced with `::` one time per address.
- Expanded: `ff02:0000:0000:0000:0000:0000:0000:0500`
- Compressed: `ff02::500`

---

## 3. Prefixes

IPv6 uses CIDR notation, same idea as IPv4.
- Example: `2001:db8:abcd:10::/64`
- `/64` means first 64 bits are network prefix, remaining 64 bits are interface ID.

---

## 4. Common IPv6 Address Types

- **Global Unicast:** Public routable (`2000::/3`).
- **Link-Local:** Local-link only (`fe80::/10`), auto-created per enabled interface.
- **Loopback:** `::1/128`.
- **Unspecified:** `::/128`.
- **Multicast:** `ff00::/8`.
- **Anycast:** Same unicast address assigned to multiple nodes; routing picks nearest instance.

IPv6 does **not** use broadcast; multicast replaces most broadcast behavior.

---

## 5. Typical Enterprise Prefix Split

A common design is:
- **/48** global routing prefix from provider or upstream allocation
- **/64** per LAN subnet

Example from `2001:db8:1111::/48`:
- `2001:db8:1111:1::/64`
- `2001:db8:1111:2::/64`
- `2001:db8:1111:3::/64`

---

## 6. Basic IOS IPv6 Interface Setup

```nasl
Router# configure terminal
Router(config)# ipv6 unicast-routing
Router(config)# interface gigabitethernet 0/0/0
Router(config-if)# description Link to internal LAN
Router(config-if)# ipv6 address 2001:db8:caff:2::1/64
Router(config-if)# no shutdown
Router(config-if)# exit
```

### SLAAC Reminder
With `ipv6 unicast-routing` enabled and an interface configured, routers send ICMPv6 Router Advertisements (RAs). Hosts can use SLAAC to build their own IPv6 addresses automatically.
