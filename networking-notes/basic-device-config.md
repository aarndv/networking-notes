# Basic NETWORKING notes

### User EXEC mode

You will be immediately on the User EXEC mode

### Privelege EXEC mode

To enter, when you're in the user EXEC mode
```nasl
Router>enable
! or you could also do
Router>en
```

### To enter global config mode, when you're in the privilege EXEC mode
```nasl
Router# configure terminal
! or you could also do
Router# conf t
```

### To exit to privilege EXEC mode from global config mode
```nasl
Router(config)# end
Router#
```

### To exit from a subconfiguration mode back to global config mode
```nasl
Router(config-if)# exit
Router(config)#
```

### Configuring a device name
```nasl
Router(config)# hostname PHRouter
PHRouter(config)# 
```

### To add a banner when opening the device
```nasl
PHRouter(config)# banner motd @
#################################################################################################################################
							CYBERSECURITY WARNING

Unauthorized access, tampering, interception, or misuse of this device, system, or network is strictly prohibited. Violators may be subject to criminal prosecution and civil liabilities under applicable Philippine laws, including the Cybercrime Prevention Act of 2012, as well as the relevant international cyber sec and data protection regulations.

All activities may be monitored, logged and used as evidence in legal proceedings.
#################################################################################################################################
@
```
