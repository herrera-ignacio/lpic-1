# 1. Fundamentals of Internet Protocols

Should demostrate proper understanding of TCP/IP network fundamentals.

## Key knowledge areas

* Demonstrate an understanding of network masks and CIDR notation
* Knowledge of the differences between private and public "dotted quad" IP addresses.
* Knowledge about common TCP and UDP ports and services.
* Knowledge about differences and major features of UDP, TCP and ICMP.
* Knowledge of major differences between IPv4 and IPv6.
Knowledge of basic features of IPv6.

## Partial list of used files, terms and utilities

* `/etc/services`
* IPv4, IPv6
* Subnetting
* TCP, UDP, ICMP

## TOC

* Internet Protocol Suite: TCP/IP basics
    * OSI Model
    * Layered communication
* IPv4
    * Subnets, classes, masks, and CIDR
    * Private, Reserved & Public addresses
* ICPM
* TCP vs UDP
* Ports and Services
* NAT: Network Address Translation
* IPv6
    * IPv4 and IPv6 comparison


### Internet Protocol Suite: TCP/IP Basics

__Transmission Control Protocol/Internet Protocol (TCP/IP)__ is a set of protocols governing communications among all computers on the internet. More specifically, TCP/IP dictates how information should be packaged (turned into bunfles of information called packets), snet, and received, and how to get to its destination.

#### IP: Internet Protocol

Dictates the logistics of packets sent out over networks. It tells packets where to go and how to get there. IP allows any computer on the internet to forward a packet to another computer that's one oe or more intervals closer to the packet's recipient.

#### TCP: Transmission Control Protocol

Responsible for ensuring reliable transmission of data across Internet-connected networks. TCP checks packets for errors and submits requests for re-tranmissions if any are found.

#### OSI model: Open Systems Interconnection Model

Conceptual model that characterizes and standardises the communication functions of a telecommunication or computing system. Its goal is the interoperability of diverse communication systems with standard communication protocols.

The model partitions the flow of data in a communication system into __7 abstraction layers__, from the physical implementation of transmitting bits across a communication medium to the highest-level representation of data of a distributed application. Each intermediate layer serves a class of functionality to the layer above it and is server by the layer below it. Classes of functionality are realized in software by standardized __communication protocols__.

#### TCP/IP layered communication

The design of protocols in the TCP/IP model does not concern itself with strict hierarchical encapsulation and layering, it recognizes __4 broad layers__ of functionality which are derived from the operating scope of their contained protocols:

* __Application Layer__: _Deals with application network processes_. Maps to the OSI application layer, presentation layer and most of the session layer (FTP, HTTP, SMTP).
* __Transport layer__: _Host-to-host transport path_. Maps to the close function of OSI session layer as well as the OSI transport layer (TCP, UDP).
* __Internet Layer__: Internetworking range. _Uses IP to describe how packets are to be delivered_. Performs functions as those in a subset of the OSI network layer .
* __Network/Link layer__: Scope of direct links to other nodes on the local network. _This layer is concerned with building packets_. Corresponds to the OSI data link layer and may include similar functions as the physical layer and some protocols of the OSI's network layer.

### IPv4

Internet Protocol Version 4 has been the networking workhorse for many years. Every device in a IPv4 network has a 4-byte address. It is customary to write these in __dotted quad__ notation where each byte is represented as a decimal number and the numbers are separated by dots (192.168.1.5, 8.8.8.8).

Today, IPv4 address ranges are mostly managed by large entities, such as ARIN or the Department of Defense in the United States, or by regional organizations. The _Internet Assigned Numbers Authority (IANA)_ has the overall control of assignment to these entities. Our _Internet Service Provider (ISP)_ provides us with an IPv4 address.

### Subnets, classes, masks, and CIDR

A IPv4 32-bit address allows for approximately 4,000,000,000 addresses. This seems like a lot. Originally this was divided so that the high-order 8 bits represented a network number and the remaining 24 bits represented the local address within the network.

This was fine when only a few large networks existed. Fortunately, all existing networks were numbered below 64 by 1981 when the address range was redefined to have classes. The then existing networks had 0 for the high order bits and were designated as class A networks, allowing up to 127 such networks. Eventually five classes were defined:

#### Network classes

| Class                        | High-order bits | Network bits  | Remaining address bits | Number of networks | Hosts per network |
|------------------------------|-----------------|---------------|------------------------|--------------------|-------------------|
| A                            | 0               | 8             | 24                     | 128 (2^7)          | 16,777,216 (2^24) |
| B                            | 10              | 16            | 16                     | 16,384 (2^14)      | 65,536 (2^16)     |
| C                            | 110             | 24            | 8                      | 2,097,152(2^21)    | 256 (2^8)         |
| D (multicast)                | 1110            | Not specified | Not specified          | Not specified      | Not specified     |
| E (Reserved or experimental) | 1111            | Not specified | Not specified          | Not specified      | Not specified     |

The top few bits of a network address defined the class and therefore how many remaining bits made up the host address. Traffic for a device in a particular network was routed first to that network and then to the device, so the main internet routers only needed the network component of the IP address for routing. The __device component could be masked off__ (with logical AND) to obtain just the network address part using a __network mask__. For example, `255.0.0.0` is the network mask for a _Class A_ network while `255.255.255.0` is the network mask for a _Class C_ network.

#### CIDR notation

Many organizations needed more addresses than a _Class C_ network, but allocating them a whole _Class B_ network gave them far more adresses than they actually needed. With fax machines, printers, scanners, computers, and many other devices all needing an address, generated pressure on the IPv4 address allocation system.

In 1993 IETF introduced __Classless Inter-Domain Routing (CIDR)__. In this model, high-order bits still define the network number, and low-order bits define the device or host address within the network. Instead of fixed sizes, CIDR specifies the number of bits that represent the network component.

This is appended to the dotted-quad notation as a slash `/` followed by a bit count. So a former class A network address might now be specified as `36.0.0.0/8` (address mask `255.0.0.0`), while a class C one might look like `192.168.1.0/24` (address mask `255.255.255.0`). An address such as `192.168.1.0/22` (address mask `255.255.252.0`) would be for an address with 22 bits for the network component and 10 bits for the host address, making for a maximum of 1024 hosts in the network.

Thus, the former classes were divided into subnets where each subnet could more accurately match the requirements of the organization using it.

Even when an organization has a large range of IP addresses, the organization may choose to have internal subnets to ease routing.

#### Private, Reserved, and Public Addresses

Three particular ranges have been reserved by IETF and IANA as __private__ or __non-routable__ addresses:

* `10.0.0.0/8`: `10.0.0.0` to `10.255.255.255` with 16,777,216 addresses and a mask of `255.0.0.0`.
* `172.16.0.0/12`: `172.16.0.0` to `162.31.255.255` with 1,048,567 addresses and a mask of `255.240.0.0`.
* `192.168.0.0/16`: `192.168.0.0` to `192.168.255.255` with 65,532 addresses and a mask of `255.255.0.0`.

These ranges can be split into subnets for internal routing purposes. If you have a home router, you will probably use a range such as `192.168.0.0/24` which is a subnet of the `192.168.0.0/16` range.

These private address ranges cannot be used on the public internet and routers will not forward packets onto the public internet if they have private addresses.

Other range that you will see on any computer but will never see on the internet is the __local loopback range__ `127.0.0.0/8`.

Finally, IANA has reserved the `100.64.0.0/10` range for use in __carrier grade network address translation (NAT)__. 

### ICMP

The _Internet Protocol (IP)_ is a host to host datagram protocol. Datagrams may pass through many network nodes. __Delivery is not guaranteed__. In the event of errors, gateways need to communicate errors between themselves or possibly report back to the originating host. ICMP is used for this purpose, to __send error messages and operational information__ indicating usccess or failure when communicating with another IP address.

ICMP uses basic IP support as if it were a higher level protocol, but ICMP is actually an __integral part of IP__ that is implemented by every IP module.

ICMP messages can be sent for purposes such as:

* A datagram cannot reach its destination.
* A gateway does not have enough buffes to forward a datagram.
* A gateway suggests a shorter route for host to send traffic.
* A gateway detects that a host is unreachable.
* A host wants to check the route to a remote host or check that it is alive.

The `ping` command is used to check if another host is alive while the `traceroute` command is used to interrogate the route to that host.

### TCP vs UDP

Both are transport layer protocols that applications on different hosts use to communicate with each other over an IP network.

#### TCP

TCP is a robust end-to-end protocol that provides for sessions, guaranteed and in-order delivery of packets, and recovery from errors such as packet loss. It is used where reliable end-to-end communication is needed.

For example HTTP, SMTP, IMAP, POP3, FTP/SFTP, SSH, or Telnet.

#### UDP

UDP is a connectionless protocol that delivers packets from one host to another where reliability is not required. ACcordingly, it has shorter headers, no recovery, and no acknowledgments when compared to TCP.

Its uses include:

* Live video streaming where occasional frame loss is not a problem.
* Voice or audio transmission, such as for VoIP applications.
* DNS, where multiple requests might be sent out for a particular query, but one reply may suffice.
* Trivial File Transfer Protocol (TFTP) for nodes booting over a local area network.

### Ports & Services

A two-byte address called a __port__ is used to distinguish which application should receive an incoming requests. Ports therefore can range from `0` to `65535`.

Applications that use protocols such as FTP, HTTP, or SSH generally use well-known port numbers. These are assigned by IANA and the current IANA policy is to assign the same UDP and TCP port number to a given application, even if the application uses only TCP or only UDP.

Linux systems maintain a list of ports and services that use them in `/etc/services`

#### Categories

Ports from `0` to `1023` are __well-known ports__. These can only be opened by processes running with root authority so that network nodes connecting can have some level of trust in the system they connect to.

Ports from `1024` through `49151` are __registered ports__. They are registered with IANA and used by non-root applications.

Ports from `49152` through `6535` may be dynamically assigned or used for private purposes.

#### Common ports

* 20: ftp-data
* 21: ftp/fsp
* 22: ss
* 23: telnet (mail)
* 25: smtp
* 53: DNS
* 80: http
* 110: pop3
* 123: ntp
* 139: netbios-ssn
* 143: imap/imap2
* 161: snmp
* 162: snmptrap
* 389: ldap
* 443: https
* 465: urd
* 514: Shell syslog
* 636: ldaps (LDAP over SSL)
* 993: snmp-tcp-port (CISCO SNMP TCP port)
* 995: pop3s (POP-3 over SSL)

### NAT: Network Address Translation

In IPv4 networks some address ranges are reserved for private use. NAT is a process where a router can accept connection requests from the private side of the network and translate the address (and possibly the initiating port number) so that it appears t the public network as it came from a port on the ISP assigned address.

This allows connections from the private network to the public network. However, when a connection request (or datagram) from the public network arrives at the router, it can only be forwarded if the router has a table indicating which private host and port the traffic arriving on a particular port should be sent to.

Besides allowing private hosts to reach public hosts, this scheme also provides a level of shielding to the private network from malicious traffic that may be scanning for potential targets.

> Note that NAT is not part of the current LPI requirements.

### IPv6

Internet Protocol Version 6 was developed by IETF in the mid 1990s to address the limitations of IPv4. Before the widespread use of NAT, IPv4 addresses were expected to run out imminently.

IPv6 uses a 128-bit address. This allows 2^128, or approximately 3.4x10^38 addresses. Several ranges are reserved for special use or excluded completly.

In contrast to the decimal dotted quad notation used for IPv4, IPv6 addresses use eight groups of four hexadecimal digits. separated by colons. For example `fe80:0000:0000:0000:efaa:4539:a6ba:27b9`. Leading zeros can be omitted from a group and one multiple group of all zeros can be replaced by a double colon `::`. So this example could be written as `fe80:0:0:0:efaa:4539:a6ba:27b9` or ` fe80::efaa:4539:a6ba:27b9`. And if you check your own loopback address, you will see `::1`.

Many utilities such as `ping`, `dig` and `traceroute` have a `-6` or `-4` option to specify whether the command should use IPv6 or IPv4.

#### IPv4 and IPv6 comparision.

IPv6 addresses are 128 bits rather than the 32 bits of IPv6. So, the IPv6 address space is unlikely to be exhausted.

Security, in the form of IPSec was originally designed in the IPv6 protocol and mandated. RFC 6434 changed this requirement to a should rather than must, as it was unclear that IPSec would become the dominant security choice among many other psobilities.

IPv6 has a simpler packet header than IPv4. Rarely used fields have been moved to extensions and the fields that remain are of fixed length. This simplifies processing in high speed routing.

IPv4 hosts can configure themselves automatically if connected to an IPv6 network. This process is kwnown as __Stateless Address Autoconfiguration (SLAAC)__, and it replaces the __DHCP__ process of IPv4.
