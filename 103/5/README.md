# 5. Create, monitor and kill processes

Should be able to perform basic process management.

## Key knowledge areas

* Run jobs in the foreground and background.
* Signal a program to continue running after logout.
* Monitor active processes.
* Select and sort processes for a display.
* Send signals to processes

## Partial list of used files, terms and utilities

* `&`
* `bg`
* `fg`
* `jobs`
* `kill`
* `nohup`
* `ps` and `pstree`
* `top`/`htop`
* `free`
* `uptime`
* `pgrep`
* `pkill`
* `killall`
* `watch`
* `screen`
* `tmux`

## TOC

* Processes
* System information
* Signaling processes
* Background processes
* Terminal multiplexers

### Processes

`ps` is used to report a snapshot of the current processes and display information about a selection of the active processes. If you want a repetitive update of the selection and displayed information, use `top`/`htop` instead.

By default, `ps` selects all processes wiih the same effective user ID (`euid=EUID`) as the ucrrent user and associated with the same terminal as the invoker. Output is unsorted by default.

You can use `pgrep` to sarch for processes currently running on the system, based on a complete or partial process name, or other specified attributes.

#### Example: see every process on the system

```
ps -e
ps -e[f|F|ly]
```

#### Example: see every process running as root

```
ps -U root -u root u
```

#### Example: Print only the name of PID 42

```
ps -q 42 -o comm=
````

#### Example: list processes as a tree

```
pstree
```

#### Example: list running processes that match _ssh_ and are owned by root.

```
pgrep -u root ssh
```

### System information

* `free`: memory usage (`-h` to format).
* `uptime`: how long system has been running, users currently logged on, and load averages.

### Signaling processes

`pkill` will send specified signal (default `SIGTERM`) to each process that match a pattern instead of listing them on standard output. 

```
pkill [options] pattern
```

`killall` works similar to `pkill` with the difference that by default, it matches the argument name exactly (up to the first 15 characters, use `-e` option for more), making it somewhat safer to use compared to `pkill`. There are some more options differences for matching processes. Note that `killall` command for systems descendant from _Unix System V_ kills _all_ processes killable by a particular user, effectively shutting down the system if run by root.

Both `pkill` and `killall` can determine the PID for you unlike the `kill` command.

The `kill` command sends a signal (defualt `TERM`) to a process by specifying its numeric PID.

Signals can be specified in three ways:

* By number (e.g., `-9`)
* With the "SIG" prefix (e.g., `-SIGKILL`)
* Without "SIG" prefix (e.g., `-KILL`)

### Background processes

* `&`
* `jobs`
* `bg`
* `fg`
* `nohup`
* `watch`

#### Background / Foreground

We can start background processes by appending the `&` character at the end of the command.

We can list background processes with `jobs` command, which provide an id that can be used in multiple commands as `%id` (e.g., `-kill -9 %2).

We can send processes to the background with `bg` command and bring it back to foreground with `fg` using `%id`.

#### `nohup`

The `nohup` command executes another command, and instructs the system to continue running it even if the session is disconnected.

#### `watch`

The `watch` command is used to execute a program periodically, showing output and errors in fullscreen. By default, it will run every 2 seconds and watch will run until interrupted.

```
// -d to highlight differences
watch -d free -m
```

### Terminal Multiplexer

* `screen`
* `tmux`

#### `screen`

The `screen` command is absoltuely packed with options. The standard operation is to create a new window with a shell in it, run a command, and then push the window to the background. This is great for long processes you don't want to accidentally terminate by closing the terminal.

Once you've got a `screen` session running, you can create new windows and run other processes in them. You can also split your terminal window into vertical or horizontal regions, and display your various `screen` windows in one window.

You can also connect to a remote machine, start a `screen` session, and launch a process. You can disconnect from the remote host, reconnect, and your process will still be running. You can share a `screen` session between two different SSH connectiosn so two people can see the same thing, in real-time.

#### `tmux`

`tmux` allows you to have multiple windows within a single terminal window, and to jump back and forth between them. A window can be divided into panes, each of which gives you an independent command line.

You can also detach a session and it becomes a headless entity running in the background, you can even close the terminal window that launched it. Later on, you can open a new terminal window and reattach the still-running session. You can also do this over an SSH connection.
