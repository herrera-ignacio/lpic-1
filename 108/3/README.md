# 3. Mail Transfer Agent (MTA) basics

Should be aware of the commonly available MTA programs and be able to perform basic forward and alias configuration on a client host.

## Key knowledge areas

* Create e-mail aliases.
* Configure e-mail forwarding.
* Knowledge of commonly available MTA programs (postfix, sendmail, exim) without configuration.

## Partial list of used files, terms and utilities

* `~/.forward`
* sendmail emulation layer commands.
* `newaliases`
* `mail`
* `mailq`
* `postfix`
* `sendmail`
* `exim`

## TOC

* MTA basics
* `mail` command
* Mail aliases
* Forward mail
* Mail queues
* Other MTA

### MTA basics

Email on a Linux system is delivered using MTAs. Your MTA delivers mail to other users on your system and MTAs communicate with each other to deliver mail all over a group of systems or all over the world.

> _Sendmail_ is the oldest Linux MTA.

Mail Transfer Agents deliver mail between users and between systems. Most Internet mail uses _Simple Mail Transfer Protocol (SMTP)_ but local mail may be transferred through files or sockets among other possibilities. Mail is a store and forward operation, so mail is stored in some kind of file or database until a user collects it or a receiving system or communication link is available.

### `mail` command

The `mail` command is an old standby that can be used to script the sending of mail as well as receive and manage your incoming mail.

You can use `mail` interactively to send messages by passing a list of addresses, non-interactively using it as a command line tool, or with no arguments you can use it to look at your incoming mail.

Mial is stored at `/var/mail` until you read it.

### Mail aliases

You use aliases that allow you to define one or more destinations for a given user name. Destinations may be other user mail boxes, files, pipes, or commands that do further processing.

You do this by specifying aliases in `/etc/mail/aliases` or `/etc/aliases`. The general form of an alias is `name: addr_1, addr_2, ...`

#### `newaliases`

Most configuration files used by sendmail are compiled into database files. This is also true for mail aliases. You use the `newaliases` command to compile your `/etc/mail/aliases` and any included files to `/etc/mail/aliases.db`.

### Forward mail

Aliases must be managed by a system administrator. Individual users can enable forwarding of their own mail using a `.forward` file in their own home directory. The file contains plain text and does not need to be compiled.

When mail is destined for you, sendmail checks for a `.forward` file in your home directory and processes the entries the same way it processes aliases.

### Mail queues

Linux mail handling uses a store-and-forward model. Outgoing mail is also stored until a receiving server connection is available.

You can use the `mailq` command to see what mail is queued.

### Other MTA

`postfix` is perhaps the most popular, but `qmail` and `exim` are also widely used.

All of these MTAs were designed as sendmail replacements, so they all have some form of sendmail compatibility. Each can handle aliases and `.forward` files. Some provide a `sendmail` command as a frontend to the particular MTA's own command.
