# 2. Install a boot manager

Should be able to select, install and configure a boot manager.

## Key knowledge areas

* Providing alternative boot locations and backup boot options.
* Install and configure a boot loader such as GRUB legacy.
* Perform basic configuration changes for GRUB 2.
* Interact with the boot loader.

## Partial list of used files, terms and utilities

* `menu.lst`, `grub.cfg` and `grub.conf`
* `grub-install`
* `grub-mkconfig`
* MBR

## TOC

* Startup process & boot loaders
* GRUB: GNU Grand Unified Bootloader
* GRUB2
* Update from GRUB to GRUB2
* GRUB recovery
* Kernel options

### Startup & Boot loaders

BIOS/UEFI delegate control to __boot loader__ which will be responsible for loading the OS.

* __LILO__: Linux Loader, old deprecated loader.
* __GRUB (legacy)__: deprecated in favor of GRUB2.
  * Does not support UEFI.
  * Configuration: `/boot/grub/menu.lst|grub.conf`

### [GRUB: GNU Grand Unified Bootloader](https://en.wikipedia.org/wiki/GNU_GRUB)

GRUB is the reference implementation of the _Free Software Foundation_'s _Multiboot Specification_, which provides a user the choice to boot one of multiple OS installed on a computer or select a specific kernel configuration available on a particular OS's partitions.

GRUB enumerates units and partitions: first disk will be `hd0` and first partition `(hd0,0)`, other partitions will be named similarly.

#### Configuration options

* __default__: OS by default.
* __timeout__: seconds to wait for user input.

#### OS settings

* __title__: Name to show in menu.
* __root__: Indicates partition that contains the Linux OS (e.g. `root (hd1, 2)`).
* __rootnoverify__: For non-linux OS indicate partiton that contains OS.
* __chainloader__: For non-linux OS, delegate control to other boot loader or `+1` to read `rootnoverify` partiton. 
* __kernel__: Path to kernel file and boot options.
* __initrm__: Path to RAM disk, with options and drivers to boot.

### GRUB2

The main difference is that it supports UEFI firmware. It is the most commonly used boot loader for all Linux distributions.

It inherits the GRUB's nomenclature but partitions start at `1`, and some other differents as `rootnoverify` replaced by `root`.

GRUB presents a menu where the user can choose from operating systems (OS) found by grub-install. GRUB can be configured to automatically load a specified OS after a user-defined timeout. If the timeout is set to zero seconds, pressing and holding `⇧ Shift` while the computer is booting makes it possible to access the boot menu.

#### Configuration

Configuration menu __cannot be modified manually__. Options are customized in `/etc/default/grub` and other data are obtained by scripts in `/etc/grub.d`.

To generate `/boot/grub/grub.cfg`, you execute `update-grub` or `grub-mkconfig`, and to install grub2 `grub-install`.

### Update from GRUB to GRUB2

```
apt-get install grub2
nano /etc/default/grub
update-grub2
upgrade-from-grub-legacy
```

### GRUB Recovery

You can start from an installation disk, and from `Advanced Options` you can enter `Rescue mode` and enter to partition's interpeter that you had previously installed linux.

```
update-grub2
grub-install /dev/sda
```

### Kernel Options

```
nano /etc/default/grub
update-grub2
```

* __quiet__: avoid startup messages.
* __debug__: see debug messages.
* __init__: replace startup program instead of `/sbin/init` (e.g. `init=/bin/bash`).
* __single__: execute single-user mode.
* __root__: define partition that stored root OS.
* __RO__/__RW__: read-only.
* __mem__: forces RAM amount (e.g. `mem=1024M`).
