# 3. Process text streams using filters

Should be able to apply filters to text streams.

## Key knowledge areas

* Send text files and output streams through text utility filters to modify the output using standard UNIX commands found in the GNU textutils package.

## Partial list of used files, terms and utilities

* bzcat
* cat
* cut
* head
* less
* md5sum
* nl
* od
* paste
* sed
* sha256sum
* sha512sum
* sort
* split
* tail
* tr
* uniq
* wc
* xzcat
* zcat

## TOC

* Visualize text
* Select text
* Sort text
* Count text
* Reverse text
* Replace characters
* Various
* Read compress files
* Checksums

### Visualize text

* `cat`, `-n`
* `more`
* `less`
* `head -n<NUM> <FILE>`, e.g `head -n5 *.log`
* `tail <NUM> <FILE>`

### Select text

`cut` shows only a part of each line, a "vertical cut".

* `-c`: selects only specific characters (e.g. `-c 2,3,7` or `-c 2-7`)
* `-d`: indicates a separator character
* `-f`: select columns to display (works as `-c`)

### Sort text

* `sort`: sorts content of a text file, line by line
  * can be used to sort directory: `sort -n size_h`
  * `-r`: reverse
  * `-n`: numeric sort
  * `-b`: ignore leading blanks
* `uniq`: whips through text files looking for unique or duplicate lines (must be adjacent by default)
  * `sort <file> | uniq | less`
  * `-c`: count duplicates
  * `-d`: repeated lines
  * `-i`: ignore cases

### Count text

`wc` command counts words, newlines, or bytes of each input file, and outputs the result.

### Reverse lines characterwise

`rev` command in Linux is used to reverse the lines characterwise. It reverses the order of the characters in each line by copying specified files to the standard output. If no files are specificied, then standard input will read.

```
rev [option] [file...]
```

### Replace characters

`tr` is used for translating one type of characters into another, you can for example convert text into all upper cases or all lower cases.

```
tr [options] charset1 [charset2]
```

* `-d`: delete characters in the first set
* `-c` complement first set of characters (operate only on the characters not in the first set)
* `-s`: remove multiple adjacent occurence of the characters in the first set
* `-t` truncate first set

#### Convert all to upper/lower case

```
cat sample.txt | tr 'a-z' 'A-Z'
```

#### Replacing set of characters

```
cat sample.txt | tr 'ilm' 'tyz'
```

#### Remove repetitive spaces

```
cat sample.txt | tr -s " "
```

#### Replace spaces with other character

```
cat sample.txt | tr -s " " ";"
```

#### Remove all non-digit characters

```
cat sample.txt | tr -cd [:digit:]
```

### Various

* `nl`: number lines and write files, writes each FILE to standard output, with line numbers added to some or all of the files.
* `od`: convert content of input in different formats with octal format as the default. Useful when debugging scripts for unwanted changes or characters.
* `split`: allows you to split files into multiple files.
* `paste`: join files horizontally by outputting lines consisting of lines from each file, separated by tab as delimiter, to the standard output.

### Read compress files

* gzip: `zcat`
* bzip2: `bzcat`
* xz: `xzcat`

### Checksums

* `md5sum`
* `sha256sum`
* `sha512sum`
