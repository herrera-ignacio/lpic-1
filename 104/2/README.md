# 2. Maintain the integrity of filesystems

Should be able to maintain a standard filesystem, as well as the extra data associated with a journaling filesystem.

## Key knowledge areas

* Verify the integrity of filesystems.
* Monitor free space and inodes.
* Repair simple filesystem problems.

## Partial list of used files, terms and utilities

* `du`
* `df`
* `fsck`
    * `e2fsck`
    * `dosfsck`
    * `reiserfsck`
* `mke2fs`
* `dumpe2fs` & `tune2fs`
* `xfs_repair`, `xfs_fsr`, `xfs_db`.

## TOC

* Verify integrity of filesystems
    * Journaling filesystems
* Integrity of XFS filesystems
* Monitor filesystem space

### Verify integrity of file systems

#### `fsck`

Requires a different command depending on filesystem type:

* `e2fsck`: EXT family
* `dosfsck`: DOS/FAT
* `reiserfsck`: RaiserFS

And can take different options:

* `-A`: verify all listed in `/etc/fstab`

* `-C`: show progress

* `-V`: detailed output

* `-N`: indicate actions to take but don't make any change

* `-f`: force verification

* `-p`: repair automatically

#### `dumpe2fs`

Show information about filesystem (`-h` for most important information).

#### `tune2fs`

Modify options of filesystem:

* `-c`: max amount of mounts to verification
* `-i`: max time between verifications
* `-m`: percent of root reserved space

#### `mke2fs` & journaling filesystems

`mke2fs` is used to create EXT file systems. It is used by `mkfs -t ext2|3|4`.

You can also use `mke2fs -O journal_dev` to create a __journaling filesystem__, which maintains a special file called a _journal_ that is used to repair any inconsistencies that occur as the result of an improper shutdown of a computer. A journaling fs writes metadata into the jorunal before each command returns.

This is far faster than a scan of the entire HDD when rebooting, and it guarantees that the structure of the fs is always internally consistent.

### Integrity of XFS filesystems

* `xfs_repair`: equivalent of `fsck`
* `xfs_db`: depuration tool for XFS
* `xfs_fsr`: desfragment filesystem (rearrange portions of data so they are closed together)

### Monitor filesystem space

* `df` (disk free): information about system partitions, total, used and free space.
    * `-h`: format output.

* `du` (disk usage): show disk space used for a file or a directory (recursively).
    * `-h`: format output
    * `-s`: show space used by directory's content 