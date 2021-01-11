# 2. Automate system administration tasks by scheduling jobs

Should be able to use `cron` and `systemd` timers to run jobs at regular intervals and to use `at` to run jobs at a specific time.

## Key knowledge areas

* Manage `cron` and `at` jobs.
* Configure user access to `cron` and `at` services.
* Understand `systemd` timer units.

## Partial list of used files, terms and utilities

* `/etc/cron.{d, daily, hourly, monthly, weekly}/
* `/etc/at.deny`
* `/etc/at.allow`
* `/etc/crontab`
* `/etc/cron.allow`
* `/etc/cron.deny`
* `/var/spool/cron`/
* `crontab`
* `at`
* `batch`
* `atq`
* `atrm`
* `systemctl`
* `systemd-run`

## TOC

* Manage `cron` jobs
* Limiting `cron` access
* Manage `at`
* Limiting `at` access
* systemd introduction
    * What is a service unit
    * `systemctl`
    * `systemd-run`

### Manage cron jobs

The croon service checks for files in the `/var/spool/cron` (individual user cron files) and `/etc/cron.d` (system service and applications) directories and the `/etc/anacrontab` file. The contents of these files define cron jobs that are to be run at various intervals.

Each interval will have its own folder `daily`, `hourly`, etc...

The cron utility runs based on commands specified in a cron table (`crontab`). Each user, including root, can have a cron file.

Files can be created in `/var/spool/cron` using the `crontab -e` command that's also used to edit a cron file, it does not only edit a command but also restarts the _crond daemon_ when you save and exit the editor.

The username field can be used to specify the user ID a job is to have when it runs.

The `cron` command looks for all crontab files and runs through each file.

### Limiting cron access

You can limit user access by creating a  `/etc/cron.allow` whitelist file that contains a list of all users with permissions to create cron jobs. Or deny specific users with `/etc/cron.deny` blacklist file.

### Manage `at`

The `at`, `batch`, `atq` and `atrm` commands queue, examine or delete jobs for later execution.

```
at [-V] [-q queue] [-f file] [-mldbv] TIME
at -c job [job...]
atq [-V] [-q queue]
atrm [-V] job [job...]
batch [-V] [-q queue] [-f file] [-mv] [TIME]  
```

* `at`: Executes commands at a specified time.
* `atq`: Lists the user's pending jobs, unless the user is the superuser, in that case, everybody's jobs are listed.
* `atrm`: Delete jobs, identified by their job number.
* `batch`: Executes commands when system load levels permit, in other words, when the load average drops below 0.8, or the value specified in the invocation of `atrun`.

### Limiting `at` access

Works the same as with `cron` access.

### systemd introduction

_systemd_ is a software suite that provides an array of system components for Linux OS.

Its main aim is to unify service configuration and behavior across Linux distributions, systemd's primary component is a _"system and service manager"_, an init system used to bootstrap user space and manage user processes. It also provides replacements for various daemons and utilities, including device management, login management, network connection management, and event logging.

#### What is a service unit

A file with the `.service` suffix contains information about a process which is managed by systemd. It is composed by three main sections:

* `[Unit]`: information not specifically related to the type of the unit, such as the service description.
* `[Service]`: information about specific type of unit, a service in this case.
* `[Install]`: information about installation of the unit.

A service can be started, stopped, restarted or reloaded. The commands to be executed when performing each of these actions can be specified by using the related options in the `[Service]` section.

#### Creating and installing a service unit

There are basically two places in the filesystem where systemd service units are installed: `/usr/lib/systemd/system` (provided by installed packages) and `/etc/systemd/system` (own services that can override default ones).

Suppose we want to create a service which disables the wake-on-lan feature on a specific ethernet interface (ens5f5 in our case) when it is started, and re-enables it when it is stopped. We can use the `ethtool` command to accomplish the main task:

```
[Unit]
Description=Force ens5f5 ethernet interface to 100Mbps
Requires=Network.target
After=Network.target

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/usr/sbin/ethtool -s ens5f5 wol d
ExecStop=/usr/sbin/ethtool -s ens5f5 wol g

[Install]
WantedBy=multi-user.target
```

To install the service we will copy the file into a `/etc/systemd/system` directory as `wol.service`, then we will start it:

```
sudo cp wol.service /etc/systemd/system && sudo systemctl start wol.service
systemctl is-active wol.service
```

#### `systemctl`

Introspect and control the state of `systemd` system and service manager.

```
systemctl <subcommand> <argument>

# service status
systemctl status <name>

# start/stop services
systemctl start|stop|restart|kill <name>

# enable/disable (set default startup)
systemctl enable|disable <name>
systemctl is-enabled <name>
```

#### `systemd-run`

Run programs in transiet scope, service units, or path-, socket-, or timer-triggered service units.

```
systemd-run [options ...] [path/socket/timer options] command [args ...]
```

It may be used to create and start a transient `.service` or `.scope` unit and run the specified command in it. It may also be used to create and start a transient `.path`, `.socket`, or `.timer` unit, that activates a `.service` unit when elapsing.

If a command is run as _transient service unit_, it will be started and managed by the service manager like any other service, and thus show up in the output of `systemctl list-units`. It will run in a clean and detached execution environment, `systemd-run` will start the service asynchronously in the background and immediately return.

If a command is run as _transient scope unit_ (`--scope`), it will be started directly `systemd-run` and thus inherit the execution environment of the caller. it is however managed by the service manager similar to normal services. Execution in this case is synchronous, and execution will return only when the command finishes.