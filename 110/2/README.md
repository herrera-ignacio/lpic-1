# 2. Setup host security

Should know how to set up a basic level of host security.

## Key knowledge areas

* Awareness of shadow passwords and how they work.
* Turn off network services not in use.
* Understand the role of TCP wrappers.

## Partial list of used files, terms and utilities

* `/etc/nologin`
* `/etc/passwd`
* `/etc/shadow`
* `/etc/xinetd.d/`
* `/etc/xinetd.conf`
* `systemd.socket`
* `/etc/inittab`
* `/etc/init.d`
* `/etc/hosts.allow`
* `/etc/hosts.deny`

## TOC

* Linux passwords & shadow file formats
* xinetd
* Stop unnecessary services
* systemd's "Socket" units
* Control network external access
* TCP Wrappers

### Linux passwords & shadow file formats

Traditional Unix systems keep user account information, including one-way encrypted passwords, in a text file called `/etc/passwd`. As this file is used by many tools (such as `ls`) to display file ownerships, etc, by matching user id #'s with the user's names, the file needs to be world-readable. Consequentally, this can be somewhat of a security risk.

Another method of storing account information, is with the __shadow password format__. As with the traditional method, this method stores account information in the `/etc/passwd` file in a compatible format. However, the password is stored as a single "x" character. A secon file, called `/etc/shadow`, contains encrypted password as well as other information such as account or password expiration values and is readable only by the root account, therefore less of a security risk.

#### How passwords are encoded

When a user picks or is assigned a password, it is encoded with a randomly generated value called the _salt_ and a _one way hash function_. This means that any particular password could be stored in 4096 different ways. The _salt_ value is then stored with the encoded password.

When a user logs in and supplies a password, the _salt_ is first retrieved from the stored encoded password. Then the supplied password is encoded with the _salt_ value, and then compared with the encoded password. If there is a match, then the user is authenticated.

If a cracker obtains your `/etc/passwd` file, they only need to encode words for a dictionary attack with the _salt_ values actually contained in it.

#### Shadowing your passwd file

By default, some Linux distributions do not contain _Shadow Suite_ installed. The _Shadow Suite_ relocates passwords to `/etc/shadow` is set so that only _root_ will be able to read and write to it.

Some programs like `xlock` don't need to be able to change passwords, they only need to be able to verify them. These programs can either be run _suid root_ or you can set up a group _shadow_ that is allowed read only access to the file and program can be run _sgid shadow_.

By moving the passwords to the `/etc/shadow` file, we are effectively keeping the attacker from having access to the encoded passwords with which to perform a dictionary attack.

#### Shadow Suite additional features

* Configuration file to set login defaults `/etc/login.defs`.
* Utilities for adding, modifying, and deleting user accounts and groups.
* Password aging and expiration.
* Account expiration and locking.
* Shadowed group passwords (optional).
* Double length passwords.
* Better control over user's password selection.
* Dial-up passwords.
* Secondary authentication programs.

#### Why you might not want to shadow your passwd file

There are a few circumstances and configurations in which installing the _Shadow Suite_ would NOT be a good idea:

* Machine does not contain user accounts.
* Machine is running on a LAN and is using NIS to get or supply user names and passwords to other machines on the network.
* Machine is being used by terminal servers to verify users via NFS, NIS, or some other method.
* Machine runs other software that validates users, and there is no shadow version available, and you don't have the source code.

#### Disable normal user logins

If you want to prevent non-root (normal) users from connectin to the system, you can block them with the `/etc/nologin` file as well as a __nologin__ shell in Linux and set a message that explains to users what is actually happening.

Creating this file will disallow user logins, and if user attempts to log in to a system where this file exists, the contents of the file will be displayed and the user login terminated.

IF you want to block logins using nologin shell (so users can log in to the system via programs such as ftp) you can use `chsh -s /bin/nologin`.

### xinetd

xinetd (_Extended Internet Service Daemon_) is an open-source super-server daemon that manages Internet-based connectivity.

It offers a more secure alternative to the older `inetd`.

xinetd listens for incoming requests over a network and launches the appropriate service for that request. Requests are made using port numbers as identifiers and xinetd usually launches another daemon to handle the request.

xinetd features __access control__ mechanisms such as __TCP Wrapper ACLs__, extensive __logging__ capabilities, and the ability to make __services available based on time__. It can place limits on the number of servers that the system can start, and has deployable defense mechanisms to __protect against port scanners__, among other things.

#### Configuration

Configuration resides in the default configuration file `/etc/xinetd.conf` and configuration of the services it supports reside in the configuration files stored in the `/etc/xinetd.d` directory.

### Stop unnecessary services

Default installation scripts add a vast number of services to your system, which, at best, you'll probably never use, or at worst, leave ports open to external breaks-in. So to protect your system, it's best to turn off any unnecessary services.

#### Find running services

Before you can decide which services are unnecessary, you need to know which services are running.

```
su
netstat -tap > listening.services
less listening.services
```

Also, check `/etc/services` for an unfamiliar service name or port number.

Systems that use _Init_ daemon process, have their services started by init scripts which are found in `/etc/init.d`, whereas systems that use _SysV_ initialization have its configuration in `/etc/inittab` (default runlevel, what processes to start, monitor and restart if they terminate, and what actions to take when the system enters a new runlevel).

#### Commonly installed services that should not be run over Internet:

* NFS (nfsd, lockd, mound, statd, portmapper, etc): standard for sharing files.
* r* services: rsh, rlogin, rexec, rcp, etc.
* telnet server.
* ftp server.
* BIND, DNS Server package.
* Mail Transport Agent: sendmail, exim, postfix, qmail.

#### Turn off the unnecessary services

```
# initd
chkconfig <service> off
chkconfig <service> --del

# sysv
systemctl is-active <service>
systemctl stop <service>
systemctl is-enabled <service>
systemctl disable <service
```

### systemd's "Socket" units

Sockets are used so that two different processes can share data or for shuttling information from one machine to another over the network.

Besides making sockets simpler to set up, systemd dumps whatever comes in through the socket to STDIN. This means you don't have to bother with complicated socket management. The other advantage is that systemd will make sure your __socket is active only as long as necessary__, waking it up when data is incoming, and closing it down again when it is done.

> A unit configuration file whose name ends in ".socket" encodes information about an IPC or network socket or a file system FIFO controlled and supervised by systemd, for socket-based activation.

Socket units don't actually start daemons on their own. Instead, they just listen on an IP address and a port, or a UNIX domain socket, and when something connects to it, the daemon that the socket is for is started and the connection is handed to it. This is useful for making sure that daemons that take up a lot of resources but are rarely used aren't running all the time, but instead they are only started when needed.

### Control network external access

`/etc/hosts.allow` and `/etc/hosts.deny` work as an iptable control access to the machine or network from external sources. Note that both _iptables_ and _host access_ can't be used simultaneously.

### TCP Wrappers

Host-based network ACL system, used to filter network access to Internet Protocol servers on (Unix-like) operating systems.

It allows host or subnetwork IP addresses, names and/or ident query replies, to be used as tokens on which to filter for access control purposes.

When compared to host access control directives often found in daemons' configuration files, TCP Wrappers have the benefit of __runtime ACL reconfiguration__ (i.e., services don't have to be reloaded or restarted) and a generic approach to network administration.

This makes it easy to use for anti-worm scripts, to add and expire client-blocking rules, when excessive connections and/or many failed login attempts are encountered.
