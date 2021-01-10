# 1. Manage user and group accounts and related system files

Should be able to add, remove, suspend and change user accounts.

## Key knowledge areas

* Add, modify and remove users and groups.
* Manage user/group info in password/group databases.
* Create and manage special purpose and limited accounts.

## Partial list of used files, terms and utilities

* `/etc/passwd`
* `/etc/shadow`
* `/etc/group`
* `/etc/skel`
* `getent`
* `groupadd`
* `groupdel`
* `groupmod`
* `passwd`
* `chage`
* `useradd`
* `userdel`
* `usermod`
* `groups`

## TOC

* Important files (databases)
* Manage groups
* Manage users

### Important files (databases)

You can use the `getent database [key ...]` command to get entries in the important files called _databases_.

```
getent passwd <user>
getent group
```

#### `/etc/passwd`

Traditionally, the `/etc/passwd` file is used to keep track of every registered user that has access to a system.

It is a colon-separated file that contains the following information:

* Username
* Encrypted password
* User ID number (UID)
* User's group ID number (GID)
* Full name of the user (GECOS)
* User home directory
* Login shell

Example:

```
root:!:0:0::/:/usr/bin/ksh
daemon:!:1:1::/etc:
bin:!:2:2::/bin:
sys:!:3:3::/usr/sys: 
adm:!:4:4::/var/adm:
uucp:!:5:5::/usr/lib/uucp: 
guest:!:100:100::/home/guest:
nobody:!:4294967294:4294967294::/:
lpd:!:9:4294967294::/:
lp:*:11:11::/var/spool/lp:/bin/false 
invscout:*:200:1::/var/adm/invscout:/usr/bin/ksh
nuucp:*:6:5:uucp login user:/var/spool/uucppublic:/usr/sbin/uucp/uucico
paul:!:201:1::/home/paul:/usr/bin/ksh
jdoe:*:202:1:John Doe:/home/jdoe:/usr/bin/ksh 
```

#### `/etc/shadow`

An alternative authentication scheme to performing authentication against the `/etc/passwd` file.

The `/etc/shadow` file contains one netry per line, each representing a user account.

```
mark:$6$.n.:17736:0:99999:7:::
[--] [----] [---] - [---] ----
|      |      |   |   |   |||+-----------> 9. Unused
|      |      |   |   |   ||+------------> 8. Expiration date
|      |      |   |   |   |+-------------> 7. Inactivity period
|      |      |   |   |   +--------------> 6. Warning period
|      |      |   |   +------------------> 5. Maximum password age
|      |      |   +----------------------> 4. Minimum password age
|      |      +--------------------------> 3. Last password change
|      +---------------------------------> 2. Encrypted Password
+----------------------------------------> 1. Username
```

The `/etc/shadow` file should not be edited by hand, instead use a command designed for the purpose. For example, to change a user password, use the `passwd` command, and to change the password aging information, use the `chage` command.

#### `/etc/group`

This file contains basic group attributes, records for system groups.

```
Name:Password:ID:User1,User2,...,Usern
```

You should access the `/etc/group` file through the system commands and subroutines defined for this purpose.

#### `/etc/skel`

Directory `/etc/skel/` (skel is derived from the “skeleton”) is used to initiate home directory when a user is first created.

The “skeleton” directory is defined in `/etc/default/useradd` file.

### Manage groups

* `groupadd [option] group_name`
* `groupdel`
* `lsgroup`
* `mkgroup`
* `rmgroup`

To change the Name parameter, you first use the `groupadd` command to add a new entry. To verify that the group has been created, enter `sudo tail /etc/group`.

You can use the `groupdel <name>` command to remove a group.

The `groupmod [options] <name>` modifies the definition of the specified group by modifying the appropriate entry in the files `/etc/group`, `/etc/gshadow`, and `/etc/passwd`.

### Manage users

* `passwd`
* `chage`
* `useradd`
* `userdel`
* `usermod`

The `useradd`, `userdel` and `usermod` commands work similar as they groups commands versions.

If you want to add an existing user account to a group:

```
# [-G, --groups] list of supplementary groups 
# [-a, --apend] add user to the supllementary group(s), use only with the -G option.
usermod -a -G <group> <user>
usermod -a -G sudo <user>
```

While a user account can be part of multiple groups, one of the groups is always the _"primary group"_. The user's login process and files and folders the user creates will be assigned to the primary group.

```
usermod -g <group> <user>
```

And to see the groups the current user account is assigned to, just run `groups` or `id`.

#### Password management

* `passwd`
* `chage`

The `passwd` command lets you change the user password and other related functionalities.

```
# change own password
passwd 

# change root password
sudo passwd root

# force user to change password at next login
sudo passwd -e <user_name>

# get information on password
passwd -S <user_name>

# lock or unlock user accounts
sudo passwd [-l|-u] <user_name>

# delete password and make account password-less
sudo passwd -d <user_name>
```

The `chage` command is used to view and change the user password expiry information (when login is to be provided for a user for limited amount of time or when it is necessary to change login password from time to time).

```
change [options] login_user
```

For example, to specify when the password should expire for root user you can use:

```
sudo chage -e YYYY-MM-DD root
sudo chage -l root
```
