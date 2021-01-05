# 7. Search text files using regular expressions

Should be able to manipulate files and text data using regular expressions. This objective includes creating simple regular expressions containing several notational elements as well as understanding the differences between basic and extended regular expressions. It also includes using regular expression tools to perform searches through a filesystem or file content.

## Key knowledge areas

* Create simple regular expressions containing several notational elements.
* Understand differences between basic and extended regular expressions.
* Understand concepts of special characters, character classes, quantifiers and anchors.
* Use regular expressions to perform searches through a filesystem or file content.
* Use regular expressions to delete, change and substitute text.

## Partial list of used files, terms and utilities

* `grep`
* `egrep`
* `fgrep`
* `sed`
* `regex`

## TOC

* Searching for patterns (`grep`)
* Find and replace (`sed`)
* Regular expressions

### Searching for patterns

The `grep` command is a string and pattern matching utility that displays matching lines from multiple files. It also works with piped output from other commands.

#### `grep`, `egrep`, `fgrep`

Print lines that match patterns.

* `-v`: show lines that __do not match__ the patterns.
* `-l`: show only file name that has the match.
* `-n`: show line number.
* `-i`: case insensitive.
* `-c`: count matching lines.
* `-r`: search recursively in files.
* `-w`: match should be an independent word.

### Find and replace

`sed` stands for _stream editor_. It is not a text editor, through it does modify text. Instead, `sed` receives text input as a "_stream_" and edits the stream according to your instructions.

The main command in the program is used to substitute one character string for another. This makes it useful for running find and replace functions on a batch of files.

#### Example: substitute cat with dog

```
echo cat catapultcat | sed 's/cat/dog' # first match

echo cat catapultcat | sed 's/cat/dog/g'
```

#### Example: setting input and output files

```
sed 's/cat/dog/g' oldfile.txt > newfile.txt
```

#### Example: overwrite file

```
sed -i '' 's/cat/dog' oldfile.txt
sed -i.bak 's/cat/dog' oldfile.txt # create backup
```

#### Example: matching regular expressions

```
echo "123 abc" | sed 's/[0-9][0-9]*/& &/'
123 123 abc
```

#### Example: extract lines

The `-n` (quiet) options supress unmatched text.

```
sed -n '1,4p' file.txt
sed -n -e '1,4p' -e '31,34p' file.txt
sed -n '1~2p' file.txt # step by 2
```

#### Example: print lines that start with 'And'

```
sed -n '/^And /p' file.txt
```

### Regular expressions

```
grep -e <REGEXP>
```

* `^`: start of line
* `$`: end of line
* `.`: any characer
* Repetitions
    * `*`: any number of occurrences
    * `?`: zero or one
    * `+`: at least one
    * `{n}`, `{n,}`, `{n1, n2}`: interval
* `\`: treat special character as plain text
* `|`: or
* `[]`: set
* `^[]`: complement set
* Character classes: `[A-Z]`, `[0-9]`, `[:digit:]`, `[:upper:]`, `[:alpha:]`, etc...
* Shortcuts
    * `\w`: any alfanumeric character and `_`
    * `\W`: any character different from `\w`
* Word limits
    * `\<`: word start
    * `\>`: word end
    * `\b`: word start/end
    * `\B`: not `\b`
* References
    * e.g. `([ae1])s\1` means first character can be `a`,`e` or `i`, then an `s`, and lastly `\1` would mean that it has to be the same as first character.
