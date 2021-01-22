# 1. Maintain system time

Should be able to maintain the system time and synchronize the clock via NTP.

## Key knowledge areas

* Set system data and time.
* Set hardware clock to correct time in UTC.
* Configure correct timezone.
* Basic NTP configuration using `ntpd` and `chrony`.
* Knowledge of using the `pool.ntp.org` service.
* Awareness of the `ntpq` command.

## Partial list of used files, terms and utilities

* `/usr/share/zoneinfo`
* `/etc/timezone`
* `/etc/localtime`
* `/etc/ntp.conf`
* `/etc/chrony.conf`
* `date`
* `hwclock`
* `timedatectl`
* `ntpd`
* `ntpdate`
* `chronyc`
* `pool.ntp.org`

## TOC

* Clocks in a Linux system
* Time zone info
* Set time zone
* `nptd` and `ntpdate`
    * `/etc/ntp.conf`
    * `pool.ntp.org`
* `chrony`
* Set hardware clock

### Clock in a Linux system

There are two main clocks in a Linux system.

#### Hardware Clock (RTC, BIOS clock, CMOS clock)

This is a clock that runs independently of any control program running in the CPU and even when the machine is powered off. The basic purpose in a Linux system is to keep time when Linux is not running.

#### System time

This time is kept by a clock inside the Linux kernel and driven by a timer interrupt. It has meaning only while Linux is running on the machine. The System Time is the number of seconds since 00:00:00 January 1, 1970 UTC (or more succinctly, the number of seconds since 1969).

You intitialize the System Time to the time from the Hardware Clock when Linux starts up, and then never use Hardware Clock again.

### Time zone info

`timedatectl` allows you to view and change the system's time and date.

GNU libc (and thus any non-embedded Linux) system time zone is configured by symlinking the `/etc/localtime` file to a binary time zone's identifier in the `/usr/share/zoneinfo` directory. Another way to check the time zone is to view the pathy the symlink points with `ls -l /etc/localtime`. Other systems reads `/etc/timezone` instead of `/etc/localtime`.

### Set time zone

First, you need to find the time zone naming.

```
timedatectl list-timezones
```

Once you identify which time is accurate to your location:

```
sudo timedatectl set-timezone <timezone>
```

Or by changing the symlink

```
sudo rm -rf /etc/localtime
sudo ln -s /usr/share/zoneinfo/America/New_York /etc/localtime
date
```

### `ntpd` intro

`ntpd` is an os daemon that synchronises the system clock with remote NTP time servers or local reference clocks. It is a complete implementation of the _Network Time Protocol (NTP) version 4_. This enables to keep time clocks on networked computer systems synchronized to a common references over the network or the Internet. These 

A range of publicly accessible time servers provide _"Coordinated Universal Time"_ (__UTC__)

`ntpd` operates by exchanging messages with one or more servers at designated intervals ranging from about one minute to about 17 minutes. When started, it requires several exchanges while the algorithms accumulate and groom the data before setting the clock. The initial delay to set the clock can be reduced using options on the Server Options page.

In order to use `ntpd`, `chronyd` must be stopped and disabled:

```
systemctl stop chronyd
systemctl disable chronyd
yum install ntp
systemctl enable ntpd
```

#### `/etc/ntp.conf`

NTP daemon configuration file. `ntpd` reads this at system start or service restart. By default, itt usually contains public server entries and commented out examples.

`ntpd` ordinarily requires a configuration file, however, a client can discover remote servers and configure them automatically. This makes it possible to deploy a fleet of workstations without specifying configuration details specific to the local environment.

#### `pool.ntp.org` project

pool.ntp.org project is a big virtual cluster of timeservers providing reliable easy to use NTP service. It's the default "time server" for most of the major Linux distributions and many networked appliances.

#### Deprectated `ntpddate`

The purpose of `ntpdate` service is to set the clock during system boot by polling the NTP server(s) given as the _server_ arguments. This was used previously to ensure that the services started after `ntpdate` would have correct time and not observe a jump in the clock. The use of `ntpdate` is considered deprecated by most systems and now uses `ntpd` with `-g` option.

### Chrony

Chrony is a flexible implementation of _Network Time Protocol (NTP)_. It is used to synchronize the system clock from different NTP servers.

It can also be used as NTPv4 server to provide time service to other servers in the same network. It is mean to operate flawlessly under different conditions such as intermittent network connection, heavily loaded networks, changing temperatures which may affect the clock of ordinary computers.

Chrony comes with two programs:

* `chronyc`: CLI
* `chronyd`: daemon that can be started at boot time

```
systemctl status chronyd    # SystemD
/etc/init.d/chronyd status  # Init

systemctl enable chronyd    # SystemD
chkconfig --ad chronyd      # Init

chronyc tracking
chronyc sources
```

The configuration file of chrony is located at `/etc/chrony.conf` or `/etc/chrony/chrony.conf` with a sample configuration file that lists NTP servers.

### Set hardware clock

`hwclock` it is a tool for accessing Hardware Clock, it querys and sets hardware clock (RTC).

You can set hardware clock to a specified time, to system time, or set the system time from the hardware clock. You can also run `hwclock` periodically to insert/remove time from Hardware Clock to compensate for systematic drift.