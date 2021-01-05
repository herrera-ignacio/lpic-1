# 6. Modify process execution priorities

Should be able to manage process execution priorities

## Key knowledge areas

* Know the default priority of a job that is created.
* Run a program with higher or lower priority than the default.
* Change the priority of a running process.

## Partial list of used files, terms and utilities

* `nice`
* `ps`
* `renice`
* `top`

## TOC

* Niceness values
* Use and effect
* `nice`
* Check the _niceness_ of running processes

### Niceness values

Niceness values range from -20 to 19, with the former being most favorable (highest priority), while latter being least.

This priority refers to _CPU priority_, thus giving the process more or less CPU time than other processes. The __default niceness is usually 0__.

### Use and effect

`nice` becomes useful when several processes are demanding more resources than the CPU can provide. In this state, a higher-priority process will get a larger chunk of the CPU time. Only the _superuser (root)_ may set the niceness to a lower valuie (i.e. a higher priority). On Linux it is possible to change `/etc/security/limits.conf` to allow other users or groups to set low nice values

### `nice` vs `renice`

The `nice` command lets you execute a program/process with modified scheduling priority.

```
nice [OPTION] [COMMAND [ARG]...]
nice -PRIORITY COMMAND
nice -10 ./test-new
sudo nice --20 ./test-new   # need root privileges to associate a negative nice value
```

You can adjust the nice value with the `-n` command line option, which adds a set integer value to the niceness, by default, this set value is `10`.

```
# -n, --adjustment=N
nice --adjustment=5 ./test-new
```

On the other hand, the `renice` command allows you to change the scheduling priority of an already running process.

```
renice [-n] priority [[-p] pid ...] [[-g] pgrp ...] [[-u] user]
```



### Check the _niceness_ of running processes

To check the existing _scheduling priority_ before changing it. If it's about the process you're about to run, the __default scheduling priority is always 0__.

#### Example: run process and check priority

```
./test-new
ps -lu my-user | grep test-new
```