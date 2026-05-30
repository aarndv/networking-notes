# Save Configuration

### Terms
- **startup-config** - This is the saved configuration file that is stored in NVRAM. It contains all the commands that will be
used by the device upon startup or reboot. Flash does not lose its contents when the device is powered off.
- **running-config** - This is stored in Random Access Memory (RAM). It reflects the current configuration. Modifying a
running configuration affects the operation of a Cisco device immediately. RAM is volatile memory. It loses all of its
content when the device is powered off or restarted.

---

### To save your changes
```nasl
Router# copy running-config startup-config
! or
Router# wr
```

### To check your device's current configuration
```nasl
Router# show running-config
```

---

If changes made to the running config do not have the desired effect and the running-config has not yet been saved, you can restore the device to its previous configuration. To do this you can:

- Remove the changed commands individually.
- Reload the device using the `reload` command in privilege EXEC mode. Note: This will cause the device to briefly go offline, leading to network downtime.

```nasl
Router# reload
```

If the undesired changes were saved to the startup-config:
```nasl
Router# erase startup-config
```
