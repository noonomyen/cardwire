# Custom Fork Features & Modifications

This document details custom features and modifications added in this fork of Cardwire.

## User-Defined Allowed Programs (`allowed_programs`)

### Overview
In upstream Cardwire, whitelisted system applications (such as `pacman`, `dnf`, `apt`, `nix`, `systemd-udevd`) are hardcoded into the daemon.

This fork introduces the `allowed_programs` configuration option in `/etc/cardwire/cardwire.toml`, allowing users to define additional process names that should be allowed GPU access when running in Smart mode.

### Configuration
Add the `allowed_programs` array to `/etc/cardwire/cardwire.toml`:

```toml
# /etc/cardwire/cardwire.toml
allowed_programs = [
    "prio-rpc-libvir",
    "qemu-event",
    "qemu-system-x86",
    "rpc-libvirtd"
]
```

### How It Works
When `cardwired` starts up, `whitelist_programs()` reads the `allowed_programs` list from the configuration file and registers each executable process name with the eBPF blocker (`CW_ALLOWED_COMM` map).

Processes matching these names will be permitted to access GPU device nodes without being blocked by Cardwire's eBPF LSM hooks.
