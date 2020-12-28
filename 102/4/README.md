# 4. Use Debian package management

Should be able to perform package management using the Debian package tools.

## Key knowledge areas

* Install, upgrade and uninstall Debian binary package.
* Find packages containing specific files or libraries which may or may not be installed.
* Obtain package information like version, content, dependencies, package integrity and installation status.
* Awareness of `apt`.

## Partial list of used files, terms and utilities

* `/etc/apt/sources.list`
* `dpkg`
* `dpkg-reconfigure`
* `apt-get`
* `apt-cache`

## TOC

* Debian package manager (`dpkg`)
* Repositories
  * Configuration
  * `apt-cache`
  * `apt-get`
  * `aptitude`

### Debian package manager (`dpkg`)

Manages debian packages without using repositories.

* `-i`: installs a package (e.g, `dpkg -i htop_2.0.2-1_amd64.deb`).
* `-r`: removes package without configuration files (e.g `dpkg -r htop`).
* `-P`: removes package with all configuration files (e.g. `dpkg -P htop`).
* `-s`: show information and status of package (e.g. `dpkg -s htop`).
* `-I`: show information of a `.deb` file (e.g. `dpkg -I <.deb>`).
* `-l`: show all packages that match a pattern (e.g. `dpkg -l apache*`).
* `-L`: show all files that have been installed with a package (e.g. `dpkg -L htop`).
* `-S`: show packages that contain files matching a pattern (e.g. `dpkg -S mount*`).

You can use `dpkg-reconfigure` to reconfig an installed package (e.g. `dpkg-reconfigure htop`).

### Repositories

#### Configuration

You can check repositories as root (`su`) in `/etc/apt/sources.list`.

After a change in repositories, you need to update packages list with `apt-get update`.

#### `apt-cache`

We can search for installed packages with `apt-cache search <name>`.

#### `apt-get`

* Install packages with `apt-get install <name>` and remove them with `apt-get remove <name>`.
* Remove configuration files with `apt-get purge <name>`.

#### `aptitude`

May not come included in your distribution/

* CLI with `aptitude`.
* Search and get installation status with `aptitude search <name>`.

#### `synaptic`

Graphical package manager, install with `apt-get synaptic`.
