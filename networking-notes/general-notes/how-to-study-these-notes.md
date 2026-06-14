# How to Study These Networking Notes

If networking feels overwhelming, treat it like learning a city map:

- **Layer 2 (switching/MAC)** is like moving inside one neighborhood.
- **Layer 3 (IP/routing)** is like traveling between neighborhoods.
- **Routing protocols/static routes** are the rules for choosing roads.
- **Services (DHCP, DNS)** are helper systems that make travel easier.

## Suggested Learning Order

1. `osi-layers.md`
2. `mac-and-ipv4.md`
3. `ipv6-structure.md`
4. `VLSM-class-notes.md`
5. `config-notes/` (device and protocol configuration)

## How to Read Command Examples

- `Router#` means privileged EXEC mode.
- `Router(config)#` means global configuration mode.
- `Router(config-if)#` means interface configuration mode.

If a command fails, check three things first:
1. Are you in the correct mode?
2. Is the interface up (`no shutdown`)?
3. Is the addressing/mask/prefix correct?

## Lab Safety Reminder

Many examples use simple passwords for lab clarity. In real networks, use strong unique credentials, prefer SSH over Telnet, and apply AAA/security policy standards.
