# 3. Change runlevels / boot targets and shutdown or reboot system

Should be able to manage the SysVinit runlevel or systemd boot target of the system. This objective includes changing to single user mode, shutdown or rebooting the system.

Should be able to alert users before switching runlevels / boot targets and properly terminate processes.

## Key knowledge areas

* Set the default runlevel or boot target.
* Change between runlevels / boot targets including single user mode.
* Shutdown and reboot from the command line.
* Alert users before switching runlevels / boot targets or other major system events.
* properly terminate processes.
* Awareness of acpid.

## Partial list of used files, terms and utilities

* `/etc/inittab`
* shutdown
* init
* `/etc/init.d/`
* telint
* systemd
* systemctl
* `/etc/systemd/`
* `/user/lib/systemd`
* wall

## TOC

* Terminal commands
* SysVinit runlevels
* systemd boot targets

### Terminal commands

* `shutdown`: manage system shutdown.
  * `-t`: seconds to wait.
  * `shutdown +<minutes>`
  * `shutdown HH:MM`
  * `-c`: cancel scheduled shutdowns.
* `shutdown -H` / `halt` / `reboot --halt`: shutdown system without sending signal ACPI for electrical shutdown.
* `shutdown -P` / `poweroff` `halt -p`: shutdown sending ACPI signal.
* `shutdown -r` / `reboot` / `halt --reboot`: reboot system.
* `wall`: sends message to all terminals.
* `mesg y/y`: manage reception of messages.

### SysVinit runlevels

* `runlevel`: current execution level.
* `init` / `telint`: change execution level.
* `update-rc.d`: scripts that get triggered at each level.
  * `update-rc.d <service> enable <level>`
  * Scripts are stored in `/etc/rc<level>.d`

### systemd boot targets

_systemd_'s _"targets"_ are the equivalent of SysVinit's runlevels, as they group units called _"services"_ that we want to run together. Each unit is defined in files such as `<name>.service` and can be stored in different directories such as: `/user/lib/systemd/system`, `/lib/systemd/system` or `/etc/systemd/system`.

You can find more information about units and where are they stored with `man systemd.unit`.

For example, if we want to see what target is run by default:

```
ls /lib/systemd/system default.target -l
```

Or to list all services:

```
ls /lib/systemd/system/*.service
```

And we should use `systemctl` to manage most of _systemd_'s aspect:

#### SysVinit compatibility

You can see a linked target for a given SysVinit runlevel:

```
ls -l /lib/systemd/system | grep runlevel
```

#### `systemctl`

* `systemctl get-default`: get default target.
* `start/stop/status/etc... unit[.service]` i.e `systemctl restart apache2`
* `isolate unit.target`: change to selected target.
* `list-units [--type=service]`: list loaded units.
* `enable/disable`: active/deactivate an unit.
* `list-dependencies`