# 1. Perform security administration tasks

Should know how to review system configuration to ensure host security in accordance with local security policies.

## Key knowledge areas

* Audit a system to find files with the _suid/sgid_ bit set.
* Set or change user passwords and password aging information.
* Being able to use `nmap` and `netstat` to discover open ports on a system.
* Set up limits on user logins, processes and memory usage.
* Determine which users have logged in to the system or are currently logged in.
* Basic sudo configuration and usage.

## Partial list of used files, terms and utilities

* `find`
* `passwd`
* `fuser`
* `lsof`
* `nmap`
* `chage`
* `netstat`
* `sudo`
* `/etc/sudoers`
* `su`
* `usermod`
* `ulimit`
* `who`, `w`, `last`

## TOC

* Find files with the suid/sgid bit set
* Set or change user passwords and password aging
* Disabling an account
* `usermod`
* Discover open ports on your system
* Who has logged in and who is currently logged in
* Who is using files
* Use and configure sudo

### Find files with the suid/sgid bit set

Recall that the `passwd` command saves your updated password in an encrypted form in `/etc/shadow`. Only the owner (root) can read/update it, so, how does the `passwd` command manage to update it?

The answer is the `suid` permission bit in the file attributes for the `passwd` command. You can use the `stat -c %#a <file>` command to list both the usual and octal file permissions.

The `-rwsr-xr-x` permission bits include `s` in the user section, or equivalently 4000 in the octal form. This tells you that the program is run with permissions as if the root user had invoked it. That is how it is allowed to access `/etc/shadow`. Obviously any executable that can access any part of the file system with root authority is a potential security exposure.

You can search for those files:

```
find -perm -4000|wc
find / -perm /4000 -fprintf /root/suid.txt '%#m %u %p\n' 2>/dev/null
ls -l /root/suid.txt
```

### Set or change user passwords and password aging

Ue the `passwd` command to change your password. A system administrator can also use the `passwd` command to reset a forgetten password, set a temporary password that must be changed by a user during next login, specify the maximum number of days that a password can be used before it must be changed, lock a user out of the system, and other related tasks. The system administrator does not need to know a user's current password.

#### Resetting a forgotten password

```
mktemp -u Pass0-XXX
sudo echo <result> | passwd --stdin <user> --expire
```

#### Expiring a password

```
passwd -e <user>
```

### Setting password duration

```
# minimum and maximum password lifetime
passwd -n 1 -x 90 <user>

# warning period and a 14 day grace eriod
chage -W 8 -I 14 <user>

# compare settings
passwd -S <user>
passwd -S <user2>
```

#### Locking and unlocking a password

```
passwd -l <user>
password -u <user>
```

### Disabling an account

```
chage -d $(date -d '20 days ago' +%F) <user>
chage -E $(date -d '2 days ago' +%F) <user>
```

### `usermod`

Some of these functions can be done with the `usermod` command. `-L` and `-U` options lock and unlock a password by prepending a `!` in `/etc/shadow`. There are `-e` (expire date), `-f-` (inactive days), and `p` (password) options.

### Discover open ports on your system

#### `nmap`

Use the `nmap` command to probe open ports on a system. Use either a hostname or an IP address for the system to scan

```
namp localhost
```

If you don't recognize a port number, try ujsing the `grep` command with `/etc/services`:

```
grep '[[:blank:]]22\/' /etc/services
```

By default, `namp` scans 1000 or the most commonly used ports and displays only the open ones. You can check specific ports with the `-p` option.

```
nmap -p 22-25, ntp, 'http*' localhost
```

The `nmap` command first attempts to ping the target system. Some systems don't respond to a ping (CIMP ECHO). The `-Pm` option allows you to supprress the ping and continue with the scan anyway.

#### `netstat`

You can find additional information about ports on your own system using the `netstat` command. You will generally want to use parameters such as `-t` for TCP parameters, `-u` for UDP connections, or `l` for ports that are listening but not connected. 

#### `ulimit`

User limits fall into two categories: soft and hard limits.

A __soft limit__ allow users to increase or decrease their own limit to the hard limit. A __hard limit__ is exactly that limit beyond which non-system users cannot raise their own limit.

`ulimit` can be used to show various limits, and set maximum soft/hard limits such as files sizes.

> File sizes are specified in blocks which are 1024 bytes each (depending on the system).

```
# root
ulimit -sF 10000
ulimit -Hf 25000
ulimit -Sa
```

We now have a 10000 blocks soft limit and a 25000 blocks hard limit. Consider what happens if user tries to create a 30MB file.

```
# user
head -c 30M </dev/urandom >testfile
File size limit exceeded
ulimit -Sf 40000
> -su: ulimit: file size: cannot modify limit: Invalid argument
ulimit -Hf
> 25000
ulimit -Sf $(ulimit -Hf) # Set to maximum allowed
```

### Who has logged in and who is currently logged in

#### Files

Linux usually maintains two files that contain information about logged-in users.

The __utmp__ file is often located in `/var/run/utmp`. It contains information about users who are currently using the system, but there may also be other users as some programs do not use utmp logging.

The __wmtp__ file is usually located at `/var/log/wtmp`, which records all login and logout activities as well as recording system reboots with a pseudo user of `~`.

Both utmp and wtmp contain binary records in an identical format.

A third file, __btmp__, if it exists, records failed login attemps and is usually located at `/var/log/btmp`.

#### Commands

* `who`: displays information from the utmp file about currently logged-in users.
* `w`: combines information from utmp file and `/proc` file system to display more information about logged in users.
* `last`: displays data about a current or a previous login activity from the wtmp file.
* `lasstb`: displays information about failed login attmepts from the btmp file (if this file exists).

### Who is using files

#### `lsof`

`lsof` can list every open file on your system. This includes sockets and can include a large amount of data, so you generally want to have specific purpose in mind.

You can restrict output by user using the file, by name of the command using the file, and by port, among many other possibilities. You can exclude error messages caused by permission settings for the gvfs file systems using the `-e` options.

```
lsof | wc
lsof -i TCP:22
lsof -e /run/user/1000/gvfs -e /run/user/1001/gvfs /dev/sdb2 # who is stopping umount?
lsof -e /run/user/1000/gvfs -e /run/user/1001/gvfs -c cat
```

#### `fuser`

The `fuser` command is to find the process that is using one or more particular files. Use the `-a` option if you want to output for all the files in your list, even if nobody is using any of them. Use the `-k` option if you want to kill the process. Use the `-u` option to see the username associated with the process ID.

```
fuser -a -<user>/bashrc -<user2>/test-data
```

### Use and configure sudo

After you have logged in as a regular user, you can use the `su` command to temporarily become another user (if you know the password) or `sudo` (you use it with your own password) command to run a command as another user. If no username is provided, you are assumed to be a root user.

#### `su`

If you use the `-`, `-l` or `--login` options, the shell will be started as a login shell with a similar environment. If no command is provided, you enter an interactive shell.

```
su <user> -c <command>
su - <user>
```

#### `sudo`

One difference from `su`, is that it is configurable and commands that run using `sudo` can be logged.

```
sudo -u mary <command>
```

Credentials are usually cached for a configurable amount of time, usually a few minutes.

The configuration for `sudo` is usually in `/etc/sudoers` with additional files possible included from the /etc/sudoers.d` directory. These files are __read-only__ and need to be edited using the `visudo` command.

#### sudoers file syntax

```
username  ALL=(root) /usr/bin/find, /bin/rm
```

* __First column__: defines what user or group this `sudo` rule applies to, in this case to `username`. If the word in this column is preceded by a `%` symbol, it designates this vlaue as a group instead of a user.
* __Second column__: `(ALL)` defines what hosts this `sudo` rule applies to. This column is most useful when you deploy a `sudo` environment across multiple systems.
* __Third column__: set in parentheses and defines what user or users, the user in the first column can execute a command as. This value is set to `root`, which means that `username` will be allowed to execute the command specified in the last column as the root user. This value can also be set to `ALL` wildcard, which would allow `username` to run the commands as any user on the system.
* __Fourth column__: comma separated list of commands the user in the first column can run as the user(s) in the third column. In this example, we are allowing `username` to run `find` and `rm` as `root` with `sudo` privileges. This value can also be set to `ALL` wildcard.

#### Adding users best practices

Ideally, create a new file under `/etc/sudoers.d` by the name `custom` (or any name as per your requirement) and add content using `visudo`.

#### Adding sudoer definitions and logging

```
~ $ sudo cat /etc/sudoers.d/88-lpic-1
## Simple example for tutorial for LPIC-1 topic 110.1
User_Alias      INSTALLERS = ian,mary
Cmnd_Alias      PKGMGR = /usr/bin/apt
INSTALLERS     ALL = (ALL) PKGMGR

## Enable logging
Defaults     log_output
Defaults! /usr/bin/sudoreplay !log_output
Defaults! /sbin/reboot !log_output  
##

~ $ sudo visudo -c
/etc/sudoers: parsed OK
/etc/sudoers.d/88-lpic-1: parsed OK
/etc/sudoers.d/README: parsed OK
```

If other specifications are absent, sudo input or output is logged in the `/var/log/sudo-io/` directory.