# 2. Maintain system time

Should be able to configure `rsyslog`. This also includes configuring the loggign daemon to send log output to a central log server or accept log output as a central log server. Use of the `systemd journal` subsystem is covered. Also, awareness of `syslog` and `syslog-ng` alternative logging systems.

## Key knowledge areas

* Basic configuration of `rsyslog`.
* Understanding of standard facilities, priorities and actions.
* Query the systemd journal.
* Filter `systemd jorunal` data by criteria such as date, service or priority.
* Configure persistente `systemd journal` storage and journal size.
* Delete old `systemd journal` data.
* Retrieve `systemd journal` data from a rescue system or file system copy.
* Understand interaction of `rsyslog` with `systemd-journald`.
* Configuration of `logrotate`.
* Awareness of `syslog` and `syslog-ng`.

## Partial list of used files, terms and utilities

* `/etc/rsyslog.conf`
* `/var/log`
* `logger`
* `logrotate`
* `/etc/logrotate.conf`
* `/etc/logrotate.d`
* `journalctl`
* `systemd-cat`
* `/etc/systemd/journald.conf`
* `/var/log/journal`

## TOC

* `rsyslog`
* `logger`
* `logrotate`
* `journalctl`

### `rsyslog`: Rocket-fast Syslog Server

High-performance great security features and a modular design. It is able to accept inputs from a wide variety of sources, transform them, and output to the results to diverse destinations.

#### Configure the log host

1. Separate partition for `/var/log` is recommended, to aovid out of control logs from filling storage where the root of the filesystem resides.
2. Configuration file `/etc/rsyslog.conf` (uncomment UDP or TCP syslog reception lines).
3. Configure template for the incoming logs, so log entries from the remote servers don't mix with log host server's local logs.
4. Restart service `systemctl restart rsyslog`.
5. Make sure firewall allows incoming connections on port 514 `firewall-cmd --ad-port=514/udp --permanent` and `firewall-cmd --reload`.
6. Use `ifconfig` or `ip addr` to document log host server's IP address.
7. Configure a log client to forward its log files to the log host server by editing `/etc/rsyslog.conf` by adding `*.* @IP` if using UDP or `*.* @@IP` if using TCP, and restart its service `systemctl restart rsyslog`.
    * You can configure specifics logs, for example cron logs, with the following syntaxis: `cron.* @IP`.
8. Test connection with `logger Test` and `ls /var/log`.

### `logger`

The `logger` command is used to manually create a log file entry.

```
logger Test
```

You can verify log file entry by displaying the most recent entries in the `/var/log/messages`:

```
tail /var/log/messages
```

### `logrotate`

`logrotate` helps managing large numbers of log files by implementing archiving, compression, deletion, and other necessary log file management takss.

Configuration file `/etc/logrotate.conf` contains directives for how log files are to be rotated by default.

#### `/etc/logrotate.d`

Along the directivies, there is a directive to include a particular folder: `/etc/logrotate.d`. This folder is used for package-specific log rotation requests.

Packages designed to take advantage of `logrotate` drop configuration files into this directory. This modularity is in keeping with the Linux spirit and ehances the extensibility of the utility. The configuration files contain similar directives and custom log files where applicable.

### `journalctl`

`systemd` collects logs from more sources than syslogs, keeps the journal logs in binary format and gives you a command line tool to read, analyze and manipulate the logs.

`journalctl` is the command line tool that lets you interact with the journal logs, you can read logs, monitor in real time, and filter based on time, service, severity and other parameters.

The default location of journald logs is `/var/log/journal` directory.

Also, the `/etc/systemd/journald.conf` is the configuration file for the tool. Make sure the value `Storage` is set to either `auto` or `persistent`.

* `journalctl`: show journal logs in chronological order.
    * `-r`: reverse.
    * `-n <number>`: most recent number of logs.
    * `-f`: real time.
    * `--utc`: By default, logs are shhown in local time.
    * `-k`: only kernel messages.
    * `-u <service_name>`
    * `-PID|GID|UID=<number>`
    * `--since=yesterday|today|<timestamp>`
    * `--until`
* `sudo jorunalctl`: journalctl is protective about what kind of logs to show to which user.