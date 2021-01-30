# 4. Configure client side DNS

Shoud ble able to configure DNS on a client host.

## Key knowledge areas

* Query remote DNS servers.
* Configure local name resolution and use remote DNS servers.
* Modify the order in which name resolution is done.
* Debug errors related to name resolution.
* Awareness of systemd-resolved.

## Partial list of used files, terms and utilities

* `/etc/hosts`
* `/etc/resolv.conf`
* `/etc/nsswitch.conf`
* `host`
* `dig`
* `getent`

## TOC

* DNS
  * Common Record Types
* Names
* Query a remote DNS server (`host`)
  * Using a specifid DNS server
* DNS Configuration files
* Using `getent` with NSS
* Using systemd-resolvd vs init
* Debugging name resolution errors

### DNS

The _Domain Name System (DNS)_ is a decentralized and hierarchical system for managing names associated with computers attached to the internet.

The most important DNS function is to map names to IP addresses, altough the system maintains other information as well. A system of authoritative servers is used to manage top-level domains. Administrators can delegate authority over subdomains. The system is therefore both robust and fault-tolerant.

#### Common Record Types

* __A__: Address record for IPv4 address.
* __AAAA__: Address record for IPv6 address.
* __CNAME__: Canonical name or alias of one record to another. Lookup usually continues recursively.
* __MX__: Mail exchange. Maps a domain name to one or more message transfer agents (MTAs).
* __PTR__: Pointer to a CNAME. Returns the CNAME without recursing.
* __SOA__: Start of authority. Information about a DNS zone, including the primary name server, the email of the domain administrator, the domain serial number, and other information.
* __TXT__: Originally for human readable text but now often caries machine readable information.

### Names

A Linux system has a name, which is called the _hostname_, and this name is used within the system (stand-alone or connected to a network) to identify it. Usually, the same hostname will be used to identify the system if it is part of a network.

When a system is connected to a network or Internet, it has a more rigorous name as part of the DNS. A DNS name has two parts: __hostname__ and __domain name__. The __fully qualified domain name (FQDN)__ consists of the hostname followed by a period and then the domain name (myhost.mydomain). Domain names usually consist of multiple parts separated by periods.

### Query a remote DNS server (`host`)

You can use the host` command to query information from a DNS server.

```bash
host cybershields.com
host ibm.com
host google.com
```

The `host` command has a number of options, including options to search for specific types of records (`-t`) and more detailed information (`-va`).

#### Using a specific DNS server

To improve speed, DNS information is cached locally and the `systemd-resolved` daemon provides responses for the `host` command.

You can specify a name server to use, for a host query, by adding its IP address to the command.

```bash
# Using Google public DNS
host -t A lpi.org 8.8.8.8
```

### DNS configuration files

There are three configuration files in `/etc` that are concerned with DNS name resolution.

#### `/etc/hosts`

Small file that is used during boot or for isolated networks. Minimally it will contain an entry for the local host, usually as just `localhost` or `localhost.localdomain`. It may be used for adding entries for other Linux systems on the local network.

#### `/etc/resolv.conf`

This file contains an entry for at least one and up to three name servers. It can also have domain and server records as options. The domain record is either derived from your hostname, often localdomain if you get your IP address using _Dynamic Host Configuration Protocol (DHCP)_.

This file is for configuring the __usage of multiple name servers__, they will be used in the order in which you specify them. If the first server is not found, then the second server is tried and son on.

#### `/etc/nsswitch.conf`

The _Name Service Switch (NSS)_ configuration file is used by the GNU C Library and certain other applications to determine the sources from which to obtain name-service information in a range of categories, and in what order.

It controls how name resolution works when looking up various types of objects, such as host addresses and passwords.

### Using `getent` with NSS

Use the `getent` command to query various NSS data sources. You specify the database and the key you want.

```bash
getent <database> <key>
```

### Using systemd-resolvd vs init

If your system uses the systemd initialization process rather than init, there are some differences in DNS resolution to consider. The systemd-resolved service provides network name resolution and implements a stub DNS resolver. The main configuration file is `/etc/systemd/resolved.conf`1.

Needless to say, the changes to `/etc/resolv.conf` will be overriden whenever these files are regenerated. Persistent changes can be made in `/etc/systemd/resolved.conf` if you are using systemd-resolved, or in `/etc/dhclient.conf` if your `resolv.conf` file is generated by dhcp.

#### Am I using init or systemd?

```bash
head -n 2 /etc/os-release
ps -p 1
```

If you are using systemd, use the `resolvectl status` to find the actual DNS server that your systemd-resolved DNS stub resolver is using upstream.

### Debugging name resolution errors

Most DNS resolution errors are the result of misconfigured DNS servers resulting in a host or domain not being found. Your fist step is to run the `host` command with ak nown hostname to see what happens.

#### No valid name servers

```bash
tail -n 4 /etc/resolv.conf
host ibm.com
```

#### `dig`

`dig` command is extremly flexiby and, by default, provides output that is more like the verbose out from the `host` command.

```bash
dig [@server_name] type
```

Note the `@` symbol before the optional DNS server-name to use.

#### `nslookup`

The `nslookup` command is also used for debugging. It displays information that you can use to diagnose DNS infrastructure but is not part of the LPIC exam contents.
