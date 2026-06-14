# Save Configuration

## Terms
- **running-config**: Active configuration in RAM (volatile).
- **startup-config**: Saved configuration used at boot, stored in NVRAM (non-volatile).

---

## Save current changes
```nasl
Router# copy running-config startup-config
! shorthand on many platforms
Router# write memory
! shorthand
Router# wr
```

## View current active config
```nasl
Router# show running-config
```

---

## Rollback-related options

If you changed running-config and have **not** saved yet:
- Remove the commands manually, or
- Reload the device to discard unsaved changes.

```nasl
Router# reload
```

If unwanted changes were already saved to startup-config:
```nasl
Router# erase startup-config
```

Then reload and reapply the intended configuration.
