# 3. Basic network troubleshooting

Should be able to troubleshoot networking issues on client hosts.

## Key knowledge areas

* Manually configure network interfaces, including viewing and changing the configuration of network interfaces using `iproute2`.
* Manually configure routing, including viewing and changing routing tables and setting the default route using `iproute2`.
* Debug problems associated with the network configuration.
* Awareness of legacy `net-tools` commands.

## Partial list of used files, terms and utilities

* `ip`
* `hostname`
* `ss`
* `ping`
* `ping6`
* `traceroute`
* `traceroute6`
* `tracepath`
* `tracepath6`
* `netcat`
* `ifconfig`
* `netstat`
* `route`

## TOC

* iproute2
* `ip` command
* Saving and restoring interfaces and routes
* Configuring network interfaces and routes
* Debugging network configuration problems
* Legacy net-tools commands

### iproute2

iproute2 it is an open-source collection of userspace utilities for controlling and monitoring various aspects of networking in the Linux kernel, including routing, network interfaces, tunnels, traffic control, and network-related device drivers.

It contains the following: _aprd, bridge, ctstat, dcb, devlink, ip, instat, nstat, rdma, routef, routel, rtacct, rtmon, rtstat, ss, tc_ and _tipc_.

iproute21 utilities communicate with the linux kernel using the _netlink_ protocol. Some of the iproute2 utilities are often recommended over now obsolte net-tools utilities that provide the same functionality.

### `ip` command

```bash
ip [ OPTIONS ] OBJECT { COMMAND | HELP }
```

You can get lots of information with only this command... for example use `ip n` to display neighbors.

In order to transmit packets over an interface or link, you need a link, at least one IP address associated with the link, and a routing table that tells you which link to transmit packets over to a particular destination. You may use `ip link show` for displaying link information and `ip route` for route information.

You can even activate or deactivate interfaces with the ip command: `ip link set <interface_name> down|up`.

### Saving and restoring interfaces and routes.

The `ip` command can save current information to a binary file using the `save` option, `showdump` to show what is in such a binary file and restore it with `restore`.

```
ip addr save <interace> > saved-file
ip addr showdump < saved-file
```

### Configure network interfaces and routes

Changes made using `ip` command are not persistent so it is a greay way to experiment.

```
ip link help
```

### Debugging network configuration problems

#### Hostname

If want to verify hostname value: `hostname` or `hostnamectl`.

#### Link connectivity

`ping -4`, `ping -6`, `ping6` to another host. You can use `-c` to limit the number of echo requests.

It's common to check connectivity to a gateway, to another device on your corporate or local network, to a public DNS server (Google `8.8.8.8`) or to an arbitrary host on the internet. In heneral, start with the local itnerface and then move further away.

You can ping over a specific interface with the `-I <interface>` option.

#### Routes & Paths

If you can't reach a host using `ping`, you might try either `traceroute` or `tracepath`. Both are designed to probe a possible path to a destination. `tracepath` is newer and designed to not require privileged authority and with less options.

Some systems have `traceroute6` and `tracepath6` with the obvious meanings.

You can specify an interface with the `traceroute -i` option, but it is not possible with `tracepath`.

#### Sockets and ss

`ss` command is part of the `iproute2` package. Use it to dump socket statistics.

Another socket tool you can use is `netcat` (`nc` or `ncat`). This is somewhat similar to the `cat` command in that you can use it to transmit text, command output, or files to another host.

### Legacy net-tools commands

Before iproute2 there was net-tools.

Let's see a comparision of old and new commands:

| New command                   | Legacy command                                                      |
|-------------------------------|---------------------------------------------------------------------|
| ip a                          | ifconfig -a                                                         |
| ip link set <ifce> up\|down   | ifconfig <ifce> up\|down                                            |
| ip addr add <CIDR IP>         | ifconfig add <IP> dev <device> ifconfig <ifce> netmask <class mask> |
| ip r                          | route                                                               |
| ip route add default via <ip> | route add default gw <ip>                                           |
| ip neigh                      | arp -a                                                              |
| ss                            | netstat                                                             |