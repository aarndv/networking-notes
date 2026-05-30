# Password Configurations

### Securing user EXEC mode access through console line
```nasl
SW(config)# line console 0
SW(config-line)# password cisco
SW(config-line)# login
SW(config-line)# exit
```

### Securing privileged EXEC mode access
```nasl
SW(config)# enable secret cisco123
```

### Securing VTY line access
```nasl
! Take note that `line vty <first> <last>` defines the virtual lines to configure. The range indicates how many concurrent remote sessions the device can support.
! For example, `line vty 0 4` supports up to 5 concurrent sessions (numbered 0 through 4).
SW(config)# line vty 0 4
SW(config-line)# password telnet
SW(config-line)# login
SW(config-line)# exit
```

### To encrypt your passwords
```nasl
SW(config)# service password-encryption
```
Use `show running-config` to verify that the passwords are now encrypted.
