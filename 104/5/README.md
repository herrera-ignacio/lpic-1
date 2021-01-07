# 5. Manage file permissions and ownership

Should be able to control file access through the proper use of permissions and ownerships.

## Key knowledge areas

* Manage access permissions on regular and special files as well as directories.
* Use access modes such as `suid`, `sgid` and the Sticky Bit to maintain security.
* Know how to change the file creation mask.
* Use the group field to grant file access to group members.


## Partial list of used files, terms and utilities

* `chmod`
* `umask`
* `chown`
* `chgrp`

## TOC

* Users and groups
* Change ownership user/group (`chgrp`, `chown`)
* Permissions
* Change permissions (`chmod`)
* Default permissions (`umask`)
* Special permissions/access modes (suid, sgid, Sticky Bit)
* Access Control List (out of LPIC-1)

### Users and groups

#### Useful commands

* `ls -l`: list files with information about ownership and permissions.
* `whoami`: show current user.
* `id [user]`: show user ids.
* `groups`: show current user's group.
* `newgrp`: change current user group.
* `useradd <name>`: create new user.
* `passwd <name>`: create password for user.
* `groupadd <name>`: create new group.

### Change ownership user/group

* `chgrp`: change group of an element.
* `chown [user][:group] element`: change user and group of one or more elements.
    * `-R`: recursive.

### Permissions

There are permissions groups and permissions types.

#### Groups

1. Owner user
2. Group
3. All users

#### Types

* `r`: read file / list dir.
* `w`: write file / create|delete elements.
* `x`: execute file / open dir.
* `-`: current position permission not granted.

#### "Special file" designator

When listing files with `ls -l`, the first mode field is the _"special file"_ designator.

* `-`: regular file.
* `d`: directory.
* `c`: character device.
* `l`: symlink.
* `p`: named pipe.
* `s`: socket.
* `b`: block device.
* `D`: door.

### Change permissions

`chmod` command sets permissions:

```
chmod <permissions> <resource>
chmod <permissions> -R <directory>
```

There are two different ways of manipulating permissions for a file, _absolute mode_ and _symbolic mode_.

```
# absolute mode
chmod 740 file

# symbolic mode
chmod u=rwx, g=r, o-rwx file
chmod u+x file
```

#### `chmod` absolute mode

Each access level (read, write, execute) has an octal value:

* Read: 4
* Write: 2
* Execute: 1

And so, we provide 3 digits that represent the 3 different identities permissions:

```
chmod 740 file
# ugo
# 740
```

* The __7__ is assigned to the user and is the sum of 4+2+1 or read+write+execute (full access).
* The __4__ is assigned to the group and is the sum of 4+0+0 (read-only).
* The __0__ is assigned to others and means no access.

#### `chmod` symbolic mode

Symbolic mode uses symbols for permissions `rwx` and identities `ugo`. There are also operators to manipulate the permissons:

* `+`: grant a level of access.
* `-`: remove a level of access.
* `=`: set a level of access.

### Default permissions

`umask` modifies permissions by default for new files and directories.

System assigns, by default, `666` for files and `777` for directories, but it _"substracts"_ the bits from the mask defined with `umask`. 

The mask is, usually by default, `022`. Thus, new files would have `644 (RW-R--R--)` permissions and directories `755 (RWXR-X-R-X)`.

If we execute `umask`, it will print current mask, and we can change it with `umask <mask>`. For it to become permanent, we need to include the command in environment configuration (`/etc/bash.bashrc` for everyone or `~/.bashrc` for each user).

### Special permissions/access modes

* __SetUID__: program will execute with the permissions of the user that owns the file, instead of the permissions of the user that executes the file. It is represented with `s` as execution permission (instead of user `x`, it needs execution permission, otherwise it will show an `S`).

* __SetGID__: same as `SetUID` but with group permissions. In the case of directories, the files created will belong the directory's ownership group instead of the creator user's group (instead of group's `x`, it needs execution permission, otherwise it will show an `S`).

* __Sticky Bit__: Directories that has this permission, will only allow file deletion by its owner. It is represented by a `t` in the _other identity's execution permission_ (instead of other's `x`, it needs execution permission,otherwise it will show a `T`).

#### Managing special permissions

```
# symbolic
chmod u+s <file>
chmod g+s <dir>

# absolute
chmod 1<ugo> <file|dir>
```

### ACL: Access Control Lists

Apply more specific set of permissions to a file or directory, without (necessarily) changing the base ownership and permissions.

We can view the current ACL using `getfacl <dir>` and set ACL with `setfacl [option] [action/specification] file`.

The action would be `-m` (modify) or `-x` (remove), and the specification would be the user or group followed by the permissions involved.

#### Example

Suppose we have a folder that is owned by group `accounting`, and we have a new user `nacho` that wants to create a file. He won't have permission.

```
setfacl -m nacho:rwx /accounting
```

But what if we don't want this user to create files in the `/accounting` directory? Instead, we only want to let him read files there, and create new files in his own folder.

```
setfacl -m nacho:r-x /accouting
mkdir ./nacho
chown nacho:accounting ./nacho
```

Now suppose that we want to keep his work secret from other `accounting` users.

```
setfacl -m g:accounting:- ./nacho
chmod g-rwx ./nacho # still need to remove base permissions for the group owner.
```