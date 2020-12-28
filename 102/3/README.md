# 3. Manage shared libraries

Should be able to determine shared libraries that executable programs depend on and install them when necessary.

## Key knowledge areas

* Identify shared libraries.
* Identify typical locations of system libraries.
* Load shared libraries.

## Partial list of used files, terms and utilities

* `ldd`
* `ldconfig`
* `/etc/ld.so.conf`
* `LD_LIBRARY_PATH`

## TOC

* Shared files
* Directories
* Useful commands

### Shared libraries

Linux supports two classes of libraries:

* __Static__: bound to a program statically at compile time.
* __Dynamic/shared__: are loadded when a program is launched and loaded into memory, binding occurs at run time.
    * __Dynamically linked__: a program is linked with the shared library and the kernel loads the library (in case it's not in memory) upon execution.
    * __Dynamically loaded__: program takes full control by calling functions with the library.

### Directories

System searchs for shared libraries in:

* `/lib`, `/usr/lib`.
* Directories at `/etc/ld.so.conf`.
  * Usually includes all directories from `/etc/ld.so.conf.d/*.conf` to be more modular.
  * Needs to be manually updated with `ldconfig` command.
* Directories stored at `LD_LIBRARY_PATH` (highest importance).
* Library files indicate version in their name, and have `.so` (shared object) extension.

### Useful commands

* `ldd` shows program's required libraries.