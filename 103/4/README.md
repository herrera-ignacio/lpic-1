# 4. Use streams, pipes and redirects

Should be able to redirect streams and connect them in order to efficiently process textual data. Tasks include redirecting standard input, standard output and standard error, piping the output of one command to the input of another command, using the output of one command as arguments to another command and sending output to both stdout and a file.

## Key knowledge areas

* Redirecting standard input, standard output and standard error.
* Pipe the output of one command to the input of another command.
* Use the output of one command as arguments to another command.
* Send output to both stdout and a file.

## Partial list of used files, terms and utilities

* `tee`
* `xargs`

## TOC

* Redirects
* Pipes
* `tee`
* `xargs`

### Redirects

#### Commands

* `>`: writes to.
* `>>`: appends to.
* `2>`/`2>>`: error message write/append to.
* `&>`/`&>>`: all write/append to.

#### Theory

When you run a Linux command, there are three data stream that play part in it:

* __stdin__: source of input data. By default, it is any text entered from the keyboard.
* __stdout__: outcome of command. By default, it is displayed on the screen.
* __stderr__: error message (if any) produced by the commands. By default, stderr is also displayed on the screen.

These streams contain the data in plain text in what's called _buffer memory_. 

#### Example: send output to a file

```
echo "Hello world" > greetings.txt
cat greetings.txt
```

#### Example: redirect command output

```
cp Downloads/ ./downloads &> log.copy
cat test.txt > copy.txt
```

### Pipes

Linux pipes let you send the output of one command to another (redirect output, input or error of one process to another for further processing).

#### Commands

* `|`

#### Example: separate files of interest

```
ls | grep ".png"
```

#### Example: count files of interest

```
ls | grep ".png" | wc -l
```

### `tee`

The `tee` command reads from standard input, and writes to files or standard output. It copies data from standard input to _each_ file, and also standard input, thus duplicates its input routing it to multiple outputs at once.

It is named after T-splitter in plumbing, which splits water into two directions and is shaped like an uppercase T.

#### Example: list .txt files, count and write to file

```
ls -1 *.txt | wc -l | tee count.txt
```

### `xargs`

The `xargs` command reads lines of text from the standard input or from the output of another command and turns them into commands and execute them.

#### Example: file list size

```
cat file_list.txt | xargs du -h
```

#### Example: find text files that contain 'red'

```
find . -type f -name "*.txt" | xargs grep -l red
```

#### Example: create multiple dirs

```
echo "one two three" | xargs mkdir
```
