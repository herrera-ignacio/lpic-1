# 2. Persistent network configuration

Should be able to manage the persistent network configuration of a Linux host.

## Key knowledge areas

* Understand basic TCP/IP host configuration.
* Configure ethernet and wi-fi network using NetworkManager.
* Awareness of `systemd-networkd`.

## Partial list of used files, terms and utilities

* `/etc/hostname`
* `/etc/hosts`
* `/etc/nsswitch.conf`
* `/etc/resolv.conf`
* `nmcli`
* `hostnamectl`
* `ifup`
* `ifdown`

## TOC

* TCP/IP host configuration
* Finding other hosts
* Activating and deactivating interfaces
* `systemd-network` daemon

### TCP/IP host configuration

Linux system has a host name, which will be used to idetnfy the system if it is part of a network. When the system is connected to a network or Internet, it has a more rigorous name as part of the DNS (a host name and a domain name). The __fully qualified domain name (FQDN)__ consists of the host name followed by a period and the domain name (such as myhost.mydomain).

#### `/etc/hostname` & `/etc/hosts`

The kernel sets the host name value during boot, usually from configuration files (`/etc/hostname`) and most siystems have the `hostname` command to display/set the hostname.

The hostname and FQDN may also be stored in `/etc/hosts`.

#### `hostnamectl`

If your system uses systemd service manager, you can use the `hostnamectl` command to show status, set host names, or set other values.

#### `nmcli`

`nmcli` is a network manager command-line interface.

* `nmcli connection show`: dispaly active network interfaces.
* `nmcli general hostname`: update `/etc/hostname`.

And you can do many more things such as using an static IP address for a specific device.

### Finding other hosts

#### `/etc/hosts`

The `/etc/hosts` file serves as a minimal address book for host names and will always have an IPv4 entry for localhost and often localhost.localadmin. If IPv6 is enabled, you will have additional entries for it.

#### `/etc/resolv.conf`

It is the resolver configuration file, used to configure DNS name servers.

#### `/etc/nsswitch.conf`

This is used to determine the sources from which to obtain name-service information in a range of categories and in what order.

For example, the line `hosts:  files dns` instructs to look up hostnames and IP addresses first in the `/etc/hosts` file and contact the DNS server if no host has been found.

### Activating and deactivating interfaces

In addition to the graphical means of activating and deactivating interfaces:

* `ifdown|ifup <interface_name>`
* `nmcli connection down|up <interface_name>`

### `systemd-network` daemon

systemd has included network capability in the form of `systemd-networkd` and `systemd-resolved` daemons for network management and DNS resolution.

Your system may have both Network Manager and systemd-netwowrkd installed. To check if Network Manager is running use `systemctl status NetworkManager`.

The cconfiguration files used by systemd-networkd have an extension of `.network` and are stored in `/usr/lib/systemd/network`, `/run/systemd/network` and `/etc/systemd/network`.

To switch from Network Manager to systemd-networkd, use the `systemctl` command to first disable Network manager and then enable and start systemd-networkd and systemd-resolved.
