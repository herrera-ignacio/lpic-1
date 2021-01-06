# 7. Find system files and place files in correct location

Should be thoroughly familiar with the _FHS (Filesystem Hierarchy Standard)_, including typical file locations and directory classifications.

## Key knowledge areas

* Understand the correct locations of file under the FHS (see [102.1 FHS section](../102/1)).
* Find files and commands on a Linux system.
* Know the location and purpose of important file and directories as defined in the FHS.

## Partial list of used files, terms and utilities

* `find`
* `locate`
* `updatedb`
* `whereis`
* `which`
* `type`
* `/etc/updatedb.conf`

## TOC

* Find files
* Execute commands on found files

### Find files

#### `find`

`find` searchs recursively in a directory for all files that match a pattern.

* `-name`: match expression.
* `-iname`: case insenstivie match.
* `-type`: file type.
    * `d`: directory.
    * `f`: files.
    * `l`: symlinks.
* `size+/-<n>`: max/min size (`c`, `k`, `M`, `G`).
* `perm[-|+|/]<mode>`: by permission.
* `-user <user>`: by ownership user.
* `-group <user>`: by ownership user's group.
* `-mmin [+/-]<n>`: last modified n minutes ago..
* `-mtime [+/-]<n>`: last modified nx24 hours.
* `-maxdepth <n>`: nested less than n levels of directories.

#### `locate` & `updatedb`

If you want to search a file on your system trough command line, and speed is the topmost priority, then you should use `locate`.

It doesn't read the filesystem, but it actually refers to a database (prepared by the command `updatedb`) to find what user is looking for and based on that search, produces its output.

The main issue is that after every new file/dir is created on the system, you need to update the tool's database for it to work correctly.

```
sudo updatedb
```

You can manually tweak configuration file for `updatedb`: `/etc/updatedb.conf`.

Lines must be of the following form:

```
VARIABLE = "VALUE"
```

When a directory is matched by variables `PRUNEFS`, `PRUNENAMES` or `PRUNEPATHS`, `updatedb` does not scan the contents of the directory. The path of the directory itself is, however, entered in the created database.

In some implementations, `/etc/updatedb.conf` is a shell script, which allows much more flexibility in defining the variables.

#### `whereis`

`whereis` command locates the binary, source, and manual page files for a command/specified files in a list of standard Linux places.

```
/{bin,sbin,etc}
/usr/{lib,bin,old,new,local,games,include,etc,src,man,sbin
```

#### `which`

`which` shows the full path of (shell) commands's executables that would have been executed when this argument had been entered at the shell prompt.

It does this by searching for an executable or script in the directories listed in the environment variable `PATH` using the same algorithm as `bash`.

#### `type`

The `type` utility shall indicate how each argument would be interpreted if used as a command name.

This allows us to find out if a command resolves to an alias, a disk file, a shell function, a built-in command, or a reserved world.

### Execute commands on found files

`file ... -exec <command>` lets you define a command to be executed with each file found. The string `{}` is replaced by file names, and `;` indicates end of command.

#### Example

```
find /etc/ -iname '*.conf' -size -1M -exec cp ;{}' /home/copies/ ';'
```