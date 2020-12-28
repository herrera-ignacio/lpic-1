# 5. Use RPM and YUM package management

Should be able to perform package management using RPM, YUM and Zypper.

## Key knowledge areas

* Install, re-install, upgrade and remove packages using RPM, YUM, and Zypper.
* Obtain information on RPM packages such as version, status, dependencies, integrity and signatures.
* Determine what files a package provides, as well as find which package a specific file come from.
* Awareness of `dnf`.

## Partial list of used files, terms and utilities

* `rpm`
* `rpm2cpio`
* `/etc/yum.conf`
* `/etc/yum.repos.d`
* `yum`
* `zypper`

## TOC

* `rpm`: RPM Package Manager
* `rpm2cpio`
* `yum`: Yellowdog Updater
    * Repositories
    * Commands
* `dnf`: Dandified YUM
* `zypper`: Zypp package manager CLI

### RPM Package Manager (`rpm`)

`rpm` manages Red Hat & related distributions such as Fedora or CentOS.

* `-i`: installs a package given a full path to a `.rpm` file.
* `-U`: installs or updates.
* `-F`: updates only if installed.
* `h`: indicates operation progress.
* `v`: shows progress for each package.
* `-e`: removes a package.
* `-V`: verifies a package (installed, last changed, etc).
* `-q`: consult about a package (e.g `rpm -ql htop`).
  * `a`: all.
  * `i` specific.
  * `f`: show packages that contain a specific file.
  * `l`: show files installed by a package.
  * `p`: information about a not installed package.

### `rpm2cpio`

Transforms a `.rpm` file into `cpio` archive.

* Listing files in a package file.
* Extracting one or more files from a package file.

#### Transform rpm to cpio

```
rpm2cpio <name.rpm> > <name>.cpio
```

#### Extract content of package

```
cpio -i --make-directories < htop.cpio
```

### Yellowdog Updated (`yum`)

Free and open-source command-line package-management utility for Linux OS using RPM Package Manager. Through YUM has a CLI, several other tools provide GUI to YUM functionality.

#### Repositories

Uses repositories configured in `/etc/yum.repos.d` (e.g. `CentOS-Base.repo`).

#### Commands

* `update`
* `install`
* `remove`
* `check-update`
* `upgrade`: updates to last versions based on cofigured repositories.
* `clean`: clean cache.
* `list`: List packages.
  * `all`
  * `available`
  * `updates`
  * `installed`
  * `obsoltes`
  * `recent`
* `repolist`: list active repositories.
* `info`: info about a specific package.
* `search`: search a package that matches a pattern in its name or description.

### Dandified YUM (`dnf`)

Package manager for rpm-based distribution, an evolution of YUM, introduced in Fedora 18 in 2013. It has been the default package manager since Fedora 22 in 2015 and Red Hat Enterprise Linux 8.

#### `zypper`

Zypper is the native CLI of the Zypp package manager. It has been used in openSUSE since version 10.2 (release in 2006).
