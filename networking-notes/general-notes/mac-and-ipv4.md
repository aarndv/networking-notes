# MAC and IPv4 Addressing Fundamentals

## 1. MAC Addressing (Layer 2)

A MAC address identifies a network interface on a local Layer 2 network (for example, inside one switched LAN).

> Analogy: A MAC address is like an apartment number inside one building.

### MAC Properties
- **Length:** 48 bits (12 hexadecimal digits).
- **Common formats:** `MM:MM:MM:SS:SS:SS`, `MM-MM-MM-SS-SS-SS`, or `MMMM.MMMM.MMMM`.
- **Structure:**
  - **OUI (first 24 bits):** Vendor identifier assigned by IEEE.
  - **Device-specific part (last 24 bits):** Assigned by the vendor.

### Ethernet Destination Types
- **Unicast:** One sender to one destination.
- **Multicast:** One sender to selected group members.
- **Broadcast:** One sender to all hosts in the local broadcast domain (`FF:FF:FF:FF:FF:FF`).

---

## 2. IPv4 Addressing (Layer 3)

An IPv4 address is a logical Layer 3 identifier used for routing between networks.

> Analogy: If MAC is the apartment number, IPv4 is the street/city address used by delivery routes.

### IPv4 Properties
- **Length:** 32 bits.
- **Format:** Dotted decimal with four octets.
- **Octet range:** 0 to 255 (binary `00000000` to `11111111`).

### Network and Host Parts
The subnet mask (or prefix) splits the address into:
- **Network portion** (used by routers for forwarding decisions)
- **Host portion** (identifies a specific interface in that subnet)

---

## 3. Subnet Masks and CIDR

A subnet mask is a 32-bit value with contiguous 1s followed by contiguous 0s.

Devices use bitwise AND (`IP AND mask`) to derive the network ID.

| Mask (Decimal) | Mask (Binary) | Prefix |
| :--- | :--- | :--- |
| `255.0.0.0` | `11111111.00000000.00000000.00000000` | `/8` |
| `255.255.0.0` | `11111111.11111111.00000000.00000000` | `/16` |
| `255.255.255.0` | `11111111.11111111.11111111.00000000` | `/24` |
| `255.255.255.192` | `11111111.11111111.11111111.11000000` | `/26` |

---

## 4. Special Addresses Inside a Subnet

- **Network address:** First address in subnet (all host bits = 0).
- **Broadcast address:** Last address in subnet (all host bits = 1, IPv4 only).
- **Usable hosts:** Addresses between network and broadcast.

Usable host formula:

$$2^h - 2$$

Where `h` is the number of host bits.

---

## 5. Classful Ranges (Historical Context)

Classful addressing is mostly historical, but still useful for exam context:
- **Class A:** `1.0.0.0` to `126.255.255.255` (default `/8`)
- **Class B:** `128.0.0.0` to `191.255.255.255` (default `/16`)
- **Class C:** `192.0.0.0` to `223.255.255.255` (default `/24`)
- **Class D:** `224.0.0.0` to `239.255.255.255` (multicast)
- **Class E:** `240.0.0.0` to `255.255.255.255` (reserved/experimental)

Notes:
- `127.0.0.0/8` is loopback.
- `0.0.0.0` can represent an unspecified address or default route context (`0.0.0.0/0`).

---

## 6. Public vs Private IPv4 (RFC 1918)

Private ranges are not routed on the public Internet:
- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

### APIPA / Link-Local IPv4
If DHCP fails on many operating systems, clients may self-assign from `169.254.0.0/16`.
