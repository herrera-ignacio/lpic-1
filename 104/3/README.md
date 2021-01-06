# 3. Control mounting and unmounting of filesystems

Should be able to configure the mounting of a filesystem.

## Key knowledge areas

* Manually mount and unmount filesystems.
* Configure filesystem mounting on bootup.
* Configure user mountable removable filesystems.
* Use of labels and UUIDs for identifying and mounting filesystems.
* Awareness of `systemd` mount units.


## Partial list of used files, terms and utilities

* `/etc/fstab`
* `/media`
* `mount`
* `umount`
* `blkid`
* `lsblk`

## TOC

* Mount filesystems
* Unmount filesystems
* Information about block devices
* Automount

### Mount filesystems

In Linux, we mount a storage device or filesystem, making it accessible and attaching it to an existing directory structure.

All files accessible in Unix-style systems, are arranged in one big tree: the _file hierarchy_, rooted at `/`. These files can be spread out over several devices.

For this, we use the `mount` command.

```
mount -t type device dir
mount [-lhV]
mount -a [-fFnrsvw] [-t vfstype] [-O optlist]
mount [-fnrsvw] [-o option[,option]...] device|dir
mount [-fnrsvw] [-t vfstype] [-o options] device|dir
```

If only __directory__ or __device__ is given, for example `mount /dir`, then `mount` looks for a corresponding mountpoint entry (and then, if not found, for a corresponding device) in the `/etc/fstab` file, and attempts to mount it.

Usually, directory used is `/media` or `/mnt`.

### Unmount filesystems

The `umount` command _"unmounts"_ a mounted filesystem, informing the system to complete any pending R/W operations, and safely detaching it.

```
umount [-dflnrv] {dir|device}
umount [-hV]
umount -a [-dflnrv] [-t vfstype] [-O options]
...

### Information about block devices

* `lsblk`: Show all block devices connected.
* `blkid`: Locate/print block device attributes.

### Automount

File `/etc/fstab` configure devices to be mounted automatically.

```
<file system> <mount point> <type> <options> <dump> <pass>
```

If needed, we can mount manually by using `mount <mount point>` as command will check for mountpoint entry.
