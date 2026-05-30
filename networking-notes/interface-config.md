# Interface Configurations for a router and switch

---

### To add an ip or ipv6 on a router interface
**ipv4**: Use `ip address <ipv4> <subnet mask>` when you're in the interface configuration.
```nasl
R1(config)# interface gi0/0/0
R1(config-if)# ip address 192.168.1.2 255.255.255.0
R1(config-if)# no shut
R1(config-if)# exit
R1(config)
```
**ipv6**: Use `ipv6 address <ipv6>/<subnet>` when you're in the interface configuration.
```nasl
R2(config)# interface gi0/0/1
R2(config-if)# ipv6 address 2001:db8::ff00:42:8329/64
R2(config-if)# no shut
R2(config-if)# exit
R2(config)
```
