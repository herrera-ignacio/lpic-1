# 103.3 Perform basic file management

Should be able to use basic Linux commands to manage files and directories.

## Key knowledge areas

* Copy, move, and remove files and directories individually.
* Copy multiple files and directories recursively.
* Remove files and directories recursively.
* Use simple and advanced wildcard specifications in commands.
* Using find to locate and act on files based on type, size or time.
* Usage of `tar`, `cpio` and `dd`.

## Partial list of used files, terms and utilities

* `cp`
* `find`
* `mkdir`
* `mv`
* `ls`
* `rm`
* `rmdir`
* `touch`
* `tar`
* `cpio`
* `dd`
* `file`
* `gzip`
* `gunzip`
* `bzip2`
* `bunzip2`
* `xz`
* `unxz`
* file globbing

## TOC

* Create, copy, move and delete
* Wildcard usage
* Compress files and directories

### Create, copy, move and delete

* `mkdir <DIR_NAME> [extra_dirs]`, `mkdir -p <DIR_NAME/NESTED_DIR_NAME>`
* `touch <FILE_NAME>`
* `cp <FILE_NAME> [more_files] <TO_PATH>`, `cp -r <SOURCE_DIR> <DEST_DIR>` (can't create nested directories)
* `rm`, `-r`, 
* `mv <FILE_PATH> <TO_PATH>`
* `dd`
* `file`: show type of data a file contains

#### `dd`

Copies a file, converting the format of the data in the process, according to the operands specified.

* `if=<origin_path>`
* `of=<dest_path>`
* `bs`: block size
* `count`: how many blocks to copy
* `conv`: convert the file as per the comma separated symbol list (ascii, block, lcase, etc...)

```
dd if=/dev/sdb1 of=/home/user/usb.img
dd if=/dev/zero of=file bs=1M count=100
```

### Wildcard usage

* __\*__: 0 or more characters (`ls -l tux*`, `ls -l *.gif`, `ls -l *-*`)
* __?__: single character (`ls *.???` would match `demo.txt` but not `video.mpeg`)
* __[]__: range of characters (`ls -l [sv]*` would match `secondfile` and `video.mpeg`)

#### Copy images of nested directories example

```
find . -type f -name \*.jpg -exec cp \{\} $TARGETFOLDER \;
find . -name "*.jpg" -exec cp /{/} [target folder path] \;
```

### Compress files and directories

#### `tar`

Compress files and directories in a single file.

* Basic compress: `tar -cf <OUTPUT_NAME>.tar <DIRS>` or with gzip `tar -czf <OUTPUT_NAME>.tar.gz <DIRS>`.
* Basic extract: `tar -xf <TAR_FILE>` or gzip `tar -xzf <OUTPUT_NAME>.tar.gz`.
* Partial backup: `-n <YY-MM-DD>`: copy diferences from an specific date.
* `-c`: compacts.
* `-x`: expands/extracts.
* `-f`: writes or reads from file.
* `-z`: compress/decompress with _gunzip_.
* `-j`: compress/decompress with _bzip2_.
* `-P`: use absolute paths (by default routes are relative).
* `-p`: preserve files permissions.
* `-r`: add elements to a compressed file.
* `-t`: show information about a tar file.
* `-v`: verbose.

#### `tar`: incremental backups

1. Create a backup with a snapshot file `tar --listed-incremental <SNAPSHOT_FILE> -czpf <OUTPUT>.tar.gz <DIRS>`
2. Then can create level 1 backup with `tar --listed-incremental <SNAPSHOT_FILE> -czpf <OUTPUT_INCREMENTAL>.tar.gz <DIRS>`: copy only modified files since last copy.
3. If further level 1 backups want to be made, you need to use copies of original snapshot file, otherwise, you'll do incremental backups from your last incremental backup (level 2, level 3, etc...).
4. To restore backups, you need to restore full backup first and then incremental backup over it: `tar -xzpf <FIRST_BACKUP> && tar --incremental -xzpf <INCREMENTAL_BACKUP>`

#### `cpio`

Stands for _"copy in, copy out"_. It is used for processing archive files like `*.cpio` or `*.tar`. It can copy files to and from archives.

* __Copy-out__: `cpio -o <name-list> archive`
  * To create a `*.cpio` file containing files and directory with `-o` to create a new file and `-v` to list files present: `cpio -ov < name-list > archive`, e.g. to copy current directory `ls | cpio -ov > compress.cpio`.
  * To create a `*.tar` archive: `ls | cpio -ov -H tar > archive`.
  * To create a copy of txt files: `find . -iname "*.txt" | cpio -ov > archive`.
* __Copy-in__: `cpio -i < archive`
    * To extact a `*.cpio` file: `cpio -iv <archive`.
    * To extract a `*.tar` file: `cpio -iv -F <TAR_FILE>`.
* __Copy pass__: copy files named in name-list to destination-directory `cpio -p destination-directory < name-list`
* `-t`: Print table of contents of all inputs present.

#### `gzip`, `bzip`, and `xz`

You can indicate multiple files to compress, use `-k` to preserve originals, and `-d` or use prefix `un` (e.g. `ungzip`) to decompress.
