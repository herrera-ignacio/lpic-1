# 2. Boot the system

Should be able to guide the system through the booting process.

## Key knowledge areas

* Provide common commands to the boot loader and options to the kernel at boot time.
* Demonstrate knowledge of the boot sequence from BIOS/UEFI to boot completion.
* Understanding of _SysVinit_ and _systemd_.
* Awareness of _Upstart_.
* Check boot events in the log files.

## Partial list of used files, terms and utilities

* dmesg
* journalctl
* BIOS/UEFI
* bootloader
* kernel
* initramfs
* init
* SysVinit
* systemd

## TOC

* BIOS
* EFI/UEFI
* Boot sequence
* MBR: Master Boot Record
* GPT: GUID Partition Table
* GRUB 2
* Boot Processes
  * SysVinit
  * systemd
  * Upstar
* dmesg
* initramfs & initrd

### BIOS

Firmware in ROM/PROM, that works as a basic I/O system.

It consists of two fundamental parts:

* __Setup__: configure options.
* __POST__: assess correct functionality of important components for startup.

### EFI/UEFI

Unified Extensible Firmware Interface. It is a specification that defines a software interface between an OS and platform firmware, replacing the legacy BIOS (but providing support for it).

Intel developed the original EFI specifications. In 2005, UEFI deprecated EFI 1.10, and its specification is managed by the _Unified EFI Forum_.

#### Advantages

* Backward and forward compatibility.
* Support for legacy BIOS systems.
* Ability to use large disk partitions (over 2TB) with a GUID partition table (GPT).
* Friendly and flexible environment pre-OS environment, including network capability, GUI and multi language.
* Modular design.
* Secure Boot option.

#### Disadvantages

* Higher abstraction layer and ability to run UEFI applications opens the doors to rootkit.
* Many argue that UEFI introduces an unnecessary complexity that won't be used by OS anyway. Not many mainline OSes take advantage of CPU-independent drivers nor the flexible and modular design, and every OS needs a native driver.

### Boot sequence

1. BIOS firmware / UEFI.
2. POST checks for hardware working properly.
3. Look for a _Boot loader_ depending on the order we configured the devices boot sequence.
  * If using BIOS, it reads the first sector of the MBR driver where the code for searching the bootloader is stored.
  * IF using UEFI, it executed the bootloader  that is stored in a special partition (ESP).

#### Boot loader

When a computer is turned off, its software‍—‌including operating systems, application code, and data‍—‌remains stored on non-volatile memory.

When the computer is powered on, it typically does not have an operating system or its loader in random-access memory (RAM). The computer first executes a relatively small program stored in read-only memory (ROM) along with a small amount of needed data, to access the nonvolatile device or devices from which the operating system programs and data can be loaded into RAM.

The small program that starts this sequence is known as a _bootstrap loader, bootstrap or boot loader_. This small program's only job is to load other data and programs which are then executed from RAM. Often, multiple-stage boot loaders are used, during which several programs of increasing complexity load one after the other in a process of chain loading.

### MBR: Master Boot Record

Special type of _boot sector_ at the very beginning of partitioned computer mass storage devices. The concept of MBRs was publicly introduced in 1983 with PC DOS 2.0.

The MBR holds the information on how the logical partitions, containing file systems, are organized on that medium. The MBR also contains executable code to function as a loder for the installed operating system, usually referred to as a _boot loader_.

The organization of the partition table in the MBR limits the maximum addressable storage aspace of a partitioned disk to _2 TiB_. Therefore, the MBR-based partitioning scheme is in the process of being supersedded by the _GUID Partition Table (GPT)_ scheme in new computers, which can coexist with an MBR in order to provide some limited form of backward compatibility for older systems.

MBRs are note present on non-partitioned media such as floppies or other storage devices configured to behave as such.

### GUID

Standard for the layout of partition tables of a physical computer storage device, such as a HDD or SSD, using _universally unique identifiers_, which are also known as _Globally Unique Identifiers (GUIDs)_. Forming part of the _UEFI_ standard, it is nevertheless also used for some BIOS systems, because of the limitations of MBR partition tables. GUID can support up to _9.4 ZiB_.

All modern PC OS support GPT. Some, including macOS and Microsoft Windows on the x86 architecture, support booting from GPT partitions only on systems with EFI firmware, but FreeBSD and most Linux distributions can boot from GPT partitions on systems with either firmware interface, legacy BIOS or modern EFI.

```
parted -l
```

### GRUB 2 (GRand Unified Bootloader)

GRUB 2 is the default free boot loader for many linux distributions. As the computer starts, GRUB 2 either presents a menu and awaits user input or automatically transfers control to an operating system kernel. It has completely rewritten GRUB to provide the user significantly increased flexibility and performance.

### Boot Processes

Once the grub boot loader loads the kernel and it has finished its startup procedure, it will hand over control to a startup manager.

#### SysVinit

On systems based on SysVinit, __init__ is the first process that is executed once the Linux kernel loads. This process is responsible for coordinating the start of the rest of the system services, configuring the user environment and essentialy bringing the system to a functional state.

* File `/etc/inittab` contains basic configuration.

* Different execution levels are defined, and __rc__ script is invoked (`/etc/rc.d` or `/etc/init.d`) with a specific execution level.

* __rc__ executes the files in `/etc/rc<level>.d/` by numeric order. Those files are links that start with `S` (start) or `K` (stop) and that point to a script in `/etc/init.d`.


#### systemd

_systemd_ is a suite of basic building blocks for a Linux system. It provides a system and service manager that runs as PID 1 and starts the rest of the system. It uses configuration files for each service/unity to manage.

The main command used to introspect and control _systemd_ is `systemctl`.

* `systemctl status`
* `systemctl list-units [--type=service]`
* `systemctl --failed`
* `systemctl status <pid|unit>`
* `systemctl start/stop/status unit[.service]`
* `systemctl enable/disavble`

_systemd_ uses its own log manager `journalctl`.

* `journalctl`
  * `-S`: since.
  * `-U`: until.
  * `YYYY-MM-DD [HH:MM:SS]]`, `yesterday`, `today`, `tomorrow`, `N day ago`, `+/- NhMmin` (i.e `-1h15min`)
  * `-u <unit>`
  * `-k`: kernel.
  * `-p`: emerg, alert, critc, err, warning, notice, info, debug.


#### Upstar

> Maintenance mode only. No new features are being developed and general advice would be move over to another minimal init system or systemd.

Upstar is an event-based replacement for the `/sbin/init` daemon which handles starting of tasks and services during boot, stopping them during shutdown and supervising them while the system is running.

### `dmesg`

You can use `dmesg` to see boot logs (drivers and operating system loading).

* `-T`: more explit time data.
* `-k`: kernel.
* `-l` filter by type of alert.
* `H`: filter with colors.

### `initramfs`: Initial RAM File System & `initrd`: Initial RAM Disk

Scheme for loading a temporary root file system into memory, which may be used as part of the Linux startup process. `initrd` and `initramfs` refer to two different methods of achieving this. Both are commonly used to make preparations before the real root file system can be mounted.

Many Linux distros ship a single, genering Linux kernel image, specifically to boot on a wide variety of hardware. The device drivers for this generic kernel image are included as loadable kernel modules because statically compiling many drivers into one kernel causes the kernel image to be much larger, perhaps too large to boot on computers with limited memory. This then raises the problem of detecting and loading the modules necessary to mount the root file system at boot time, or for that matter, deducing where or what the root file system id.

To further complicate matters, the root file system may be on a software RAID volume, LVM, NFS, or an encrypted partition. All of these require special preparations to mount.

Another complication is kernel support for hibernation, which suspends the computer to disk by dumping an image of the entire contents of memory to a swap partition or a regular file, then powering off. On next boot, this image has to be made accessible before it can be loaded back into memory.

To avoid having to hardcore handling for so many special cases into the kernel, an initial boot stage with a temporary root file-system, now dubbed _early user space_, is used. This root file system can contain user-space helpers which do the hardware detection, module loading and device discovery necessary to get the real root file-system mounted.

You can find it in the GRUB configuration file in a similar way:

```
initrd /boot/initrd.img-<version>-amd64
```