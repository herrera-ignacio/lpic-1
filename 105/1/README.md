# 1. Customize and use the shell environment.

Should be able to customize shell environments to meet users' needs. Candidates should be able to modify global and user profiles.

## Key knowledge areas

* Set environment variables (e.g. PATH) at login or when spawning a new shell.
* Write Bash functions for frequently used sequences of commands.
* Maintain skeleton directories for new user accounts.
* Set command search path with the proper directory.

## Partial list of used files, terms and utilities

* `.`
* `source`
* `/etc/bash.bashrc`
* `/etc/profile`
* `env`
* `export`
* `set`
* `unset`
* `~/.bash_profile`
* `~/.bash_login`
* `~/.profile`
* `~/.bashrc`
* `~/.bash_aliases`
* `~/.bash_logout`
* `function`
* `alias`

## TOC

* Files for environmental items (such as umask, `PATH`, etc...)
* Environmental variables
* Bash Aliases
* Bash Functions

### Files for environmental items

You can list environmental variables with `env`.

#### `~/.profile` and `~/.bash_profile`

Used to set environmental items for a __user's shell__.

#### `/etc/profile`

Used to set __system wide__ environmental variables on users shells. The variables are sometimes the same ones that are in the `.bash_profile`, however this file is used to set an initial `PATH` or `PS1` for all shell users of the systems.

This is only executed for __interactive/login shells__ (can simulate with `bash -l`).

#### `/etc/profile.d`

`/etc/profile` will execute the scripts within `/etc/profile.d/*.sh`. If you plan on setting your own system wide environmental variables, it is recommended to place your configuration in a shell script within this directory.

#### `~/.bashrc`

Used to set __command aliases and functions__ used by __bash shell users__. This will run whenever we run `bash`.

#### `~/.bash_aliases`

Alternatively, you can isolate aliases in this file.

For this to work you need to have the following in `~/.bashrc`:

```
if [ -f ~/.bash_aliases ]; then
. ~/.bash_aliases
fi
```

#### `~/.bash_logout`

You can also customize logout behavior from login shells in `~/.bash_logout`.

#### `/etc/bashrc` or `/etc/bash.bashrc`

Just like `/etc/profile` is the system wide version of `.bash_profile`, those two files are the system wide version of `.bashrc`.

Those are executed for __both interactive and non-interactive shells__.

### Environmental variables

#### `export`

Set export attribute for shell variables.

```
# set variables
export [-fn] [name[=value] ...]

# list all exported names in the current shell
export -p
```

For example, if you want to set a colorful prompt: 

```
export PS1='\[\e[1;32m\][\u@\h \W]\$\[\e[0m\] '
```

Or if you want to export a shell function:

```
name () { echo "tutorialspoint"; }
export -f printname
```

#### `set`

Command used to defined and set/clear the flags of the __system environment__, those are specific settings within the Bash shell environment.

```
set -/+[option]
```

Use `+` to disable an option.

#### `unset`

Unset values and attributes of variables and functions.

```
unset [-fv] name
```

If `-v` is specified, _name_ refers to a vartiable name, while if `-f` is specified, _name_ refers to a function. If neither is specified, _name_ refers to a variable and if a variable by that name does not exit, it is unspecified whether a function by that name, if any, shall be unset.

Unsetting a variable or function that was not previously set shall not be considered an error and does not cause the shell to abort.

Note that `VARIABLE=` is not equivalent to an unset of `VARIABLE`, as in this example, it's being set to `""`.

### Bash Aliases

To list all aliases use `alias`, and to call them, just use an alias' name.

#### Temporal

```
alias update='sudo -- sh -c "/root/bin/chk_disk && dnf update'
```

You can remove alias with `unalias <name>` or all alias definitions with `unalias -a`.

#### Permanent

Edit `~/.bash_aliases` or `~/.bashrc` file appending your aliases and `source <file>`.

### Bash Functions

The syntax for declaring a bash function is straightforward.

```
# without reserved word

function_name () {
    commands
}

function_name () { commands; }

# with reserved word

function function_name { commands; }

# invoke function

function_name
```
