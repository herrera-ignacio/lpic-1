# 1. Create partitions and filesystems

Should be able to configure disk partitions and then create filesystems on media such as hard disks. This includes the handling of swap partitons.

## Key knowledge areas

* Manage MBR and GPT partition tables.
* Use various `mkfs` commands to create various filesystems such as:
    * ext2/ext3/ext4
    * XFS
    * VFAT
    * exFAT
* Basic feature knowledge of `Btrfs`, including multi-device filesystems, compression and subvolumes.

## Partial list of used files, terms and utilities

* `fdisk`
* `gdisk`
* `parted`
* `mkfs`
* `mkswap`

## TOC

* Disk partition table scheme
* Manage partitions
* Filesystems basics
* Manage filesystems

### Disk partition table scheme

We have MBR (Master Boot Record) used by BIOS and the GPT (Globally Unique Disk Identifier Partition Table) used by UEFI.

In the MBR scheme we can have just four primary partitions and if we need more we create a logical partition and add as many extra partitions therein, but in the GPT scheme we can have up to 128 primary partitions. There's difference also in the partition size, MBR supports 2TB partition and GPT 2^33 TB partitions.

### Manage MBR partitions

`fdisk -l` will list drives and partitions. You can see it as a tree with `lsblk`.

To manage partitions for a drive, `fdisk /dev/<NAME>` or `parted /dev<NAME>`.

#### Manage GPT partitions

`gdisk /dev/<NAME>`

### Filesystems basics

* __FAT32 - VFAT__: old and simple, most compatible with multiple OS and hardware. It cannot store files larger than 4GB and partitions can't be larger than 2TB.

* __NTFS__: FAT32 successor. Improves FAT32 limits and has more functionalities such as permissions, encryption, security copies, etc.

* __EXT4__: Last version of ext filesystem for Linux. Files can be as large as 16TB, and partitions as large as 1024PB.

* __XFS__: supports files up to 8EB and partitions of 16EB.

* __BTRFS__: one of the latest, it offers __multi-devices__ (single filesystem in multiple partitions) and __compression__.

* __SWAP__: used as alternative RAM, handled by the system. 

### Manage filesystems

`mkfs` is used to build a Linux file system on a device, usually a hard disk partition.

`mkswap` sets up a Linux swap area on a device or in a file. The device argument will usually be a disk partition.
