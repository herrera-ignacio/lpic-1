# 6. Create and change hard and symbolic links

Should be able to create and manage hard and symbolic links to a file.

## Key knowledge areas

* Create links.
* Identify hard and/or soft links.
* Copying versus linking files.
* Use links to support system administration tasks.


## Partial list of used files, terms and utilities

* `link`
* `ln`
* `ls`

## TOC

* What is a link
* `link` command
* `unlink` command
* `ln` command
* Read links

### What is a link

A __(hard) link__ is an entry in your file system which connects a file name to the actual bytes of data on te disk. When a file is creating, OS writes bytes to a location on the disk and also _linked_ that data to a file name. If you rename the file, the contents of the file are not altered, only the information that points to it.

__Symbolic/soft links__, instead of linking to the data of a file, they __link to another link__. This has several potential benefits. __Symlinks__ can link to directories, cross filesystem boundaries, and more. Removing the file/directory than a symlink points to breaks the link (it will still exist in your directory though).

### `link` command

The `link` command allow us to manually create a link to file data that already exists.

```
link <FILE> <LINKNAME>
```

Now `<LINKNAME>` points to the same data than `<FILE>`.

If we delete one of the files, we are deleting one of the links to the data, other existing pointers will remain existing.

### `unlink` command

Removes a specified file and if there is no other hard link to the file data, the file data itself is removed from the system. Just as `rm`, it is a wrapper to the `unlink` system call.

### `ln` command

`ln`, by default, creates a __hard link__ like `link` does.

```
ls FILE LINKNAME
```

However, we can also use `ln` to create __symbolic/soft links__ with the `-s` option.

```
ln -s FILE|DIRECTORY LINKNAME
```

### Read links

You can use `ls -L|--dereference` for showing information for the file the link references rather than for the link itself.

Also, you can use the `readlink` command to print the value of a symlink.
