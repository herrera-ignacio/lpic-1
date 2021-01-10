# 1. Install and configure X11

Should be able to install and configure X11.

## Key knowledge areas

* Understanding of the X11 architecture (verify video card and monitor are supported by an X server).
* Basic understanding and knowledge of the X Window configuration file.
* Overwrite specific aspects of Xorg configuration, such as keyboard layout.
* Understand the components of desktop environments, such as display managers and window managers.
* Manage access to the X server and display applications on remote X servers.
* Awareness of Wayland. 

## Partial list of used files, terms and utilities

* `/etc/X11/xorg.conf`
* `/etc/X11/xorg.conf.d/`
* `~/.xsession-errors`
* `xhost`
* `xauth`
* `DISPLAY`
* `X`

## TOC

* X11: X Window System
    * Purposes and abilities
    * Architecture
* Use X client application remotely
    * `xhost`
    * Request local display/input service (`DISPLAY` variable)
    * `xauth`
* X Configuration

### X11: X Window System

X is a _windowing system_ for bitmap displays, common on Unix-like OS.

X provides the basic framework for a GUI environment: drawing and moving windows on the display device and interacting with a mouse and keyboard.

X does not mandate the user interface (this is handled by individual programs). As such, the visual styling of X-based environments varies greatly, different programs may present radically different interfaces.

The X protocol has been at version 11 (hence __X11__) since September 1987. The _X.Org Foundation_ leads the X project, with the current reference implementation, X.Org Server, available as free and open-source software under the MIT License and similar permissive licenses.

#### Purpose and abilities

X is an architecture-independent system for remote graphical user interfaces and input device capabilities. Each person using a networked terminal has the ability to interact with the display with any type of user input device.

Unlike most earlier display protocols, X was specifically designed to be used over network connections rather than on an integral or attached display device. X features network transparency, which means an X program running on a computer somewhere on a network (such as the Internet) can display its user interface on an X server running on some other computer on the network. The X server is typically the provider of graphics resources and keyboard/mouse events to X clients, meaning that the X server is usually running on the computer in front of a human user, while the X client applications run anywhere on the network and communicate with the user's computer to request the rendering of graphics content and receive events from input devices including keyboards and mice.

The user's graphic display and input devices become resources made available by the local X server to both local and remotely hosted X client programs who need to share the user's graphics and input devices to communicate with the user.

##### Architecture client-server

X uses a client–server model: an X server communicates with various client programs. The server accepts requests for graphical output (windows) and sends back user input (from keyboard, mouse, or touchscreen). 

This client–server terminology – the user's terminal being the server and the applications being the clients – often confuses new X users, because the terms appear reversed. But X takes the perspective of the application, rather than that of the end-user: X provides display and I/O services to applications, so it is a server; applications use these services, thus they are clients.

The communication protocol between server and client operates network-transparently: the client and server may run on the same machine or on different ones, possibly with different architectures and operating systems. A client and server can even communicate securely over the Internet by tunneling the connection over an encrypted network session.

An X client itself may emulate an X server by providing display services to other clients. This is known as "X nesting". Open-source clients such as Xnest and Xephyr support such X nesting.

### Use X client application remotely

#### `xhost`

`xhost` is a server access control program for X. It adds and deletes hostnames or user names to the list allowed to make connections to the X server.

```
xhost [[+-]name ...]
```

#### Request local display/input service

If we connect to an allowed server `telnet/ssh name`, we can request local display/input service:

```
export DISPLAY="<our_ip>:0"
gedit # any application
```

The `DISPLAY` variable is used by __X11__ to identify your display, keyboard and mouse. Usually it'll be `:0` on a desktop PC, referring to the primary monitor.

#### `xauth`

`xauth` program is used to edit and display the authorization information used in connecting to the X server.

This program is usually used to extract authorization records from one machine and merge them into another.

### X Configuration

You can use `xorg` executable to manage displays and configurations.

An alternative directory for configuration files is `/usr/share/X11/`.

#### `/etc/X11/xorg.conf`

Usually generated automatically by OS. Specifics aspects of xorg can be configured here, such as keyboard layout.

#### `/etc/X11/xorg.conf.d/`

Besides `xorg.conf`, Xorg uses configuration files ending in the suffix `.conf` for its initial setup.

This directory stores host-specific configuration. Files are read in ASCII order, and b convention their names start with `XX-`. These files are parsed by the X server upon startup and are treated like part of the traditional `xorg.conf` configuration file.