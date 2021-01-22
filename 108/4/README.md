# 4. Manage printers and printing

Should be able to manage print queues and user print jobs using CUPS and the LPD compatibility interface.

## Key knowledge areas

* Basic CUPS configuration (local and remote printers)
* Manage user print queues.
* Troubleshoot general printing problems.
* Add and remove jobs from configured printer queues.

## Partial list of used files, terms and utilities

* CUPS configuration files, tools, and utilities.
* `/etc/cups`.
* lpd legacy interface (lpr, lprm, lpq).

## TOC

* CUPS: Common UNIX Printing System.
* LPD Legacy.
* Manage print queues.
* Manage user print jobs.
* Printing files.
* Printer options.
* CUPS and the CUPS server.

### CUPS: Common UNIX Printing System

CUPS is the printer and print job manager for Linux. Early computer printers typically printed lines of text in a particular character set and font size. Today's graphical printers are capable of printing both graphics and text in a variety of sizes and fonts. Nevertheless, some of the commands you use today have their history in he older line _Line Printer Daemon (LPD)_ technology.

### LPD Legacy

UNIX and Linux systems, printing intially used _Berkeley Software Distribution (BSD)_ printing subsystem, consisting of a _Line Printer Dameon (LPD)_ running as a server, and client commands such as `lpr` to submit jobs for printing. This protocol was later standardized by the IETF as RFC 1179 __Line Printer Daemon Protocol__.

System also had a printing daemon, with similar functionality but different command set, you will frequently see two commands with different options that accomplish the same task. For example `lpr` from Berkeley implementation and `lp` from System V implementation each print files.

### Manage Print queues

Users direct print jobs to a logical entity called a __print queue__. In sigle-user systems, a print queue and a printer are usually equivalent. However, CUPS allows a system without an attached printer to queue print jobs for eventual printing on a remote system, and through the use of classes to allow a print job directed to a class to be printed on the first available printer of that class.

* `lpstat`: Check queues known to CUPS.
    * `-a`: display accepting status of printers.
    * `-c`: display print classes.
    * `-p`: dispaly print status.
    * `-s`: display default printer, printers and classes.
    * `-d`: display default printer.
* `lpc status`
* `cupsdisable -r <message> <printer>
* `accept <printer>`

### Manage user print jobs

*. Find out whether any jobs are queued for a particular printer or for all printers: `lpq [-P <printer_name>] `.
*. Delete print jobs: `lprm <job_id>`.
*. Alter attributes of jobs such as priority or number of copies with `lp`.
*. `lpmove` allows jobs to be moved from one queue to another.

### Printing files.

```
echo "Print this text" > printexample.txt
lpr printexample.txt        # option 1
lp printexample.txt         # option 2
```

### Checking printer options

```
lpoptions -p <printer_name>
```

### CUPS and the CUPS Server

At the heart of the CUPS printing system is the `cupsd` print server which runs as a daemon process. The configuration file is normally located in `/etc/cups/cupsd.conf`.

The configuration file contains parameters that control things such as access to the printing system, whether remote printing is allowed, localtion of spool files, and so on.
