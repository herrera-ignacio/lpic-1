# 3. Localisation and internationalisation

Should be able to localize a system in a different lagnauge than English. As well, an understanding of why `LANG=C` is useful when scripting.

## Key knowledge areas

* Configure locale settings and environment variables.
* Configure timezone settings and environment variables.

## Partial list of used files, terms and utilities

* `/etc/timezone`
* `/etc/localtime`
* `/usr/share/zoneinfo`
* `LC_*`
* `LC_ALL`
* `LANG`
* `TZ`
* `/usr/bin/locale`
* `tzselect`
* `timedatectl`
* `date`
* `iconv`
* UTF-8
* ISO-8859
* ASCII
* Unicode

## TOC

* Time
* Language

### Timezone (TZ)

We always store timestamps in UTC time, but, we will see them in our system converted to our locale time.

#### Set time `date`

```
/etc/localtime
ln -s /usr/share/zoneinfo/<Contintent>/<Country> localtime
export TZ=:/usr/share/zoneinfo/America/Dominica
date
locale
```

#### timedatectl`

`timedatectl`, comes as part of the `systemd` system and service manager, in replace for old traditional `date` command used in `sysvinit` daemon. It allows you to query and change the configuration of the system clock and its settings, you can use this command to set or change the current date, time and timezone, or enable automatic system clock synchronization with a remote NTP server.

```
timedatectl set-timezone UTC
timedatectl set-timezone Europe/Amsterdan
```

systemd uses a combination of its own configuration files and the well-known ones.

For linux systems, there are typically two files related to the configuration:

* `/etc/localtime`: usually a symbolic link.
* `/etc/timezone`: plain text file, continent and place.

The differences are their format and type of content.

### Language

#### `LANG`

`LANG` environment variable controls localisation. It affects many standard command-line tools like `sort`, `grep`, `awk`. Setting its value to `C` tells all those tools to consider only basic ASCII characters and disable UTF-8 multibyte match.

It can make tools more performant, also, C is used to specify a _safe_ basic locale.

* `LANG=C` forces applications to use default language for output, and forces sorting to be bytewise. It's used in scripts to predict program output which may very based on current language.
* `LANG=es_ES man`

You'll typically set `$LANG` to your preference with a value that identifies your region.

#### `LC_ALL=C`

Environment variable that overrides all other localisation settings (like thousand separator or decimal point character, character set, sorting order, month, day names, language, currency symbol, etc).

#### Change language

```
sudoedit /etc/default/locale:

LANG="en_US"
LANGUAGE="en_US:en"
LC_ALL=en_US.UTF-8 

sudoedit ~/.pam_environment:

LANG=en_US
LANGUAGE=en_US
```

#### `iconv`

Command'line program and standarized API used to convert between different character endcodings, trough Unicode conversion.

`stdin` can be converted from ISO-88951-1 to current locale and output to `stdout` using:

```
iconv -f iso-8895-1
```

Similarly, an input file `infile`:

```
iconv -f iso-8859-1 -t utf-8 <infile> -o <outfile>
```
