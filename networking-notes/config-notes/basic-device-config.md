# Basic Networking Device Access Notes

## CLI Modes

### User EXEC mode
Default mode after login. Prompt usually ends in `>`.

### Privileged EXEC mode
Enter from User EXEC:
```nasl
Router> enable
! shorthand
Router> en
```

### Global configuration mode
Enter from Privileged EXEC:
```nasl
Router# configure terminal
! shorthand
Router# conf t
```

### Return to Privileged EXEC
```nasl
Router(config)# end
Router#
```

### Exit one level (submode to global config)
```nasl
Router(config-if)# exit
Router(config)#
```

## Set Hostname
```nasl
Router(config)# hostname PHRouter
PHRouter(config)#
```

## MOTD Banner
```nasl
PHRouter(config)# banner motd @
#################################################################################################################################
                            CYBERSECURITY WARNING

Unauthorized access, tampering, interception, or misuse of this device, system, or network is strictly prohibited. Violators may be subject to criminal prosecution and civil liabilities under applicable Philippine laws, including the Cybercrime Prevention Act of 2012, as well as relevant international cybersecurity and data protection regulations.

All activities may be monitored, logged, and used as evidence in legal proceedings.
#################################################################################################################################
@
```
