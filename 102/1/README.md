# 1. Design hard disk layout

Should be able to design a disk partitioning scheme for a Linux system.

## Key knowledge areas

* Allocate filesystems and swap space to separate partitions or disks.
* Tailor the design to the intended use of the system.
* Ensure the `/boot` partition conforms to the hardware architecture requirements for booting.
* Knowledge of basic features of LVM.

## Partial list of used files, terms and utilities

* `/` (root) filesystem
* `/var` filesystem
* `/home` filesystem
* `/boot` filesystem
* EFI System Partition (ESP)
* swap space
* mount points
* partitions

## TOC

* Disk partitioning
* Swap partition
* FHS: Filesystem Hierarchy Standard
* LVM: Logical Volume Manager
* RAID: Redundant Array of Inexpensive/Independent Disks

### Disk partitioning

When installing Linux OS, we need to decide which part of the disk we will use for this purpose.

With Linux, we need at least to partitions: one for the system and other for the swap space

### Swap partitioning

Swap partition serves multiple purposes:

* __Overflow space__ for your RAM. If your RAM fills up completely, any additional applications will run off the swap partition.
* __Prioritization__. Move some itemos from memory to hard drive in order to leave more room in memory for more important items (can configure _"swapiness_").
* __Hibernation__. A swap partition is used as the destination of your memory's contents whenever you tell your system to hibernate.

#### Swap space heuristics

* Without hibernation
  * < 2GB RAM: x2
  * 2GB - 5GB RAM: Total RAM + 2gb
  * \> 5GB RAM: depends on system use
* With hibernation
  * RAM + sqrt(RAM)

### FHS: Filesystem Hierarchy Standard

FHS defines the directory structure and directory contents in Linux distributions. It is maintained by the _Linux Foundation_.

In FHS, all files and directories appear under the _root directory_ `/`, even if they are stored on different or virtual devices.

#### Directory str8ct8re

* `/`: Primary hierarchy root and _root directory_ of the entire file system hierarchy.
* `/bin`: Essential command binaries that need to be avaiable in single-user mode, for all users (e.g., `cat`, `ls`, `cp`).
* `/boot`: Boot loader files (e.g. kernels, initrd).
* `/dev`: Device files (e.g. `/dev/disk0`, `/dev/sda`, `/dev/tty`).
* `/etc`: Host specific system-wide configuration files. FHS restricts this to static configuration files and may not contain binaries ("Editable Text Configuration" / "Extended Tool Chest").
  * `/etc/opt`: Configuration files for add-on packages that are stored in `/opt`
  * `/etc/sgml`
  * `/etc/X11`
  * `/etc/xml`
* `/home`: User's home directories, containing saved files, personal settings, etc.
* `/lib`: Libraries essential for the binaries in `/bin` and `/sbin`.
* `/lib<qual>` (optional): Alternate format essential libraries.
* `/media`: Mount points for removable media.
* `/mnt`: Temporary mounted filesystems.
* `/opt`: Optional application software packages.
* `/proc`: Virtual filesystem providing process and kernel information as files. Generally, automatically generated and populated by the system, on the fly.
* `/root`: Home directory for the root user.
* `/run`: Run-time variable data, information about the running system since last boot, e.g., currently logged-in users and running daemons.
* `/sbin`: Essential system binaries (e.g. `fsck`, `init`, `route`).
* `/srv`: Site-specific data served by this system, such as data and scripts for web servers, data offered by FTP servers, and repositories for version control systems.
* `/sys`: Contains infromation about devices, drivers, and some kernel features.
* `/tmp`: Directory for temporary files. Often not preserved between system reboots.
* `/usr`: _Secondary hierarchy_ for __read-only user data__, contains the majority of multi-user utilities and applications.
  * `/usr/bin`: Non-essential command binaries (not needed in single-user mode) for all users.
  * `/usr/include`: Standard include files.
  * `/usr/lib` & `/usr/lib<qual>`: Libraries for the binaries in `/usr/bin` and `/usr/sbin`.
  * `/usr/local`: _Tertiary hierarchy_ for local data specific to this host.
  * `/usr/share`: architecture-indpendent (shared) data.
  * `/usr/src`: Source code, e.g., kernel source code with its header files.
  * `/usr/X11R6`: X Windows File System, Version 11, Release 6.
* `/var`: Variable files, files whose content is expected to continually change during normal operation of the system, such as logs, spool files, and temporary e-mail files.
  * `/var/cache`: Application cache data. Such data are locally generated as a result of time-consuming I/O or calculation. Application must be able to regenerate or restore the data.
  * `/var/lib`: State information. Persistent data modified by programs as they run, e.g., databases, packaging system metadata, etc.
  * `/var/lock`: Lock files. Files keeping track of resources currently in use.
  * `/var/log`: Log files.
  * `/var/mail`: Mailbox files (some distros may store this in the deprecated `/var/spool/mail`).
  * `/var/opt`: Variable data from add-on packages.
  * `/var/run`: Run-time variable data. In FHS 3.0, this is replaced by `/run`. A system should either continue to provide a `var/run` directory or a symbolic link from it to `/run` for backwards compatibility.
  * `/var/spool`: Spool for tasks waiting to be processed, e.g., print queues and outgoing mail queue.
    * `/var/spool/mail`: Deprectaed in favor of `/var/mail`.
  *`/var/tmp`: Temporary files to be preserved between reboots.

#### Multiuser file systems

Those can be managed in different partitions for performance concerns.

* `/var`
* `/tmp`
* `/home`
* `/usr`: If not going to store many user-specific applications.

### LVM: Logical Volume Manager

> Physical Volume of different disks -> Volume Groups -> Logical Volumes

Device mapper framework that provides logical volume management for the Linux kernel. Most modern Linux distributions are LVM-aware to the point of being able to have their root file systems on a logical volume.

#### Uses

* Creating single logical volumes of multiple physical volumes or entire hard disks, allowing for dynamic volume resizing.
  * Volume groups (VGs) can be resized online by absorbing new physical volumes (PVs) or ejecting existing ones.
  * Logical volumes (LVs) can be resized online by concatenating extents onto them or truncating extents from them.
* Managing large hard disk farms by allowing disks to be added and replaced without downtime or service disruption, in combination with hot swapping.
* On small systems, instead of having to estimate at installation time how big a partition might need to be, LVM allows filesystems to be easily resized as needed.
* Performing consistent backups by taking snapshots of the logical volumes.
  * Read only snapshots of LVs (LVM1).
  * Read/write snapshots (LVM2).
* Encrypting multiple physical partitions with one password.
* LVs can be created to include RAID functionality.

### RAID: Redundant Array of Inexpensive/Independent Disks

Data storage virtualization technology that combines multiple physical disk drive components into one or more logical units for the purposes of data redundancy, performance improvement, or both.

Data is distributed across the drives in one of several ways, referred to as RAID levels, depending on the required level of redundancy and performance.

Different schemes / data distribution layouts are named by the word "RAID" followed by a number (e.g. RAID 0). Each scheme, or RAID level, provides a different balance among the key goals: 

* Reliability
* Availability
* Performance
* Capacity

RAID levels greater than RAID 0 provide protection against unrecoverable sector read errors, as well as against failures of whole physical drives.

#### RAID 0: Striping

Consists of striping, but no morroring (RAID 1) or parity.

Because striping distributes the content of each file among all drives in the set, the failure of any drive causes the entire RAID 0 volume and all files to be lost, unlike a spanned volumes (disk concatened together, end to beginning, so they appear to be a single large disk) that preserves files on the unfalling drives.

The benefit of RAID 0 is that the throughput of R/W operations to any file is multiplied by the number of drives because, unlike spanned volumes, R/W are done conrurently. The cost is increased vulnerability to drive failures.
