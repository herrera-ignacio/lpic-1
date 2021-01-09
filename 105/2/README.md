# 2. Customize or write simple scripts. 

Should be able to customize existing scripts, or write simple new Bash scripts.

## Key knowledge areas

* Use standard sh syntax (loops, tests).
* Use command substitution.
* Test return values for success or failure or other information provided by a command.
* Execute chained commands.
* Perform conditional mailing to the superuser.
* Correctly select the script interpreter through the shebang (#!) line.
* Manage the location, ownership, execution and suid-rights of scripts.

## Partial list of used files, terms and utilities

* `for`
* `while`
* `test`
* `if`
* `read`
* `seq`
* `exec`
* `||`
* `&&`

## TOC

* Shebang (#!) line
* Run scripts
* Loop operators (`seq`, `for`,  `while` statement)
* `if` statement
* Variables
* Logical operators `&&` `||`
* Test return values
* `read` command
* `exec` command

### Shebang (#!) line

Character sequence `#!` at the beginning of a script.

```bash
#!interpreter [optional-arg]
```

When a text file with a shebang is used as if it is an executable in a Unix-like OS, the program loader mechanism parses the rest of the file's initial line as an __interpreter directive__.

The loader executes the specified interpreter program, passing to it as an argument the path that was initially used when attempting to run the scirpt, so that the program may use the file as input data.

The shebang is usually ignored by the interpreter, because the `#` character is a comment marker in many scripting languages.

#### Examples

* `#!/bin/sh -x`: Bourne shell with `-x` argument.
* `#!/bin/bash`: Bash shell
* `#!/usr/bin/env python3`: Python interpreter.
* `#!/bin/false`: Do nothing but return non-zero exit status, indicating failure.

### Run Scripts

```bash
# from interpreter
bash script.sh

# as executable
chmod +x script1.sh
./script1.sh
```

You can update your path to run bash scripts from anywhere by their name, for example, that are stored in your `~/bin` directory.

```bash
export PATH=$PATH:$HOME/bin
cp script.sh bin/
script.sh # from anywhere 
```

### Loop operators

#### `seq`

```bash
seq 5
# 1...5
```

#### `for`

```bash
for u in $(seq 10) ; do
> useradd user$u
> echo user${u}:Password1 | chpasswd -c SHA512
> done
tail -n 10 /etc/shadow

for u in $(seq 10) ; do
> userdel -r user$u
> done
!tail
```

#### `while` statement

```bash
COUNT=10
while (( COUNT > 0 ))
do
    echo -e "$COUNT \c"
    sleep 1
    (( COUNT -- ))
done
echo "We have lift off!"
```

### `if` statement

```bash
# conditional depending on user input
if [[ $1 = 'd' ]]
    then
        find /etc -maxdepth 1 -type d
    else
        find /etc -maxdepth 1 -type l
fi
```

### Variables

```bash
fruit=apple
echo $fruit
// apple

bash
echo $fruit
// ''
exit

export fruit
bash
echo $fruit
apple
```

We can use `set -o allexport` to automatically export all variables.

### Test return values

We will print the output of a script to a file and return specific exit codes.

```bash
DFV=$HOME/df.txt

if [ -e $DF ]
    then
        echo "The file exists!"
        exit 2
fi

df -hT > $DF
mail -s "Disk free $(date + %F)" root < $DF
```

### Logical operators `&&` `||`

#### `&&`

```bash
#!/bin/bash
a=0
b=0
a_max=10
b_max=5
# and opertor used to form a compund expression
while [[ $a -lt $a_max+1 && $b -lt $b_max+1 ]]; do
   echo "$a"
   let a++
   let b++
done
```

#### `||`

```bash
#!/bin/bash
a=1
b=1
a_max=7
b_max=5
# and opertor used to form a compund expression
while [[ $a -lt $a_max+1 || $b -lt $b_max+1 ]]; do
   echo "$a"
   let a++
   let b++
done
```

### `read` command

`read` is a builtin command of the Bash sheel. It reads a line of text from standard input and splits it into words which can be used as the input for other commands.

#### Example 1

```bash
while read; do echo "$REPLY"; done
```

`read` takes data from the terminal. Text is echoed on the next line. This loop continues until you press `Ctrl+D (EOF)` on a new line. Because no variable names were specified, the netire line of text is stored in the variable REPLY.

```bash
while read text; do echo "$text"; done
```

#### Example 2

```echo
while read -ep "Type something: " -i "My text is " text; do
    echo "$text";
done
```

#### Example 3

```bash
echo "one two three four" | while read word1 word2 word3; do
  echo "word1: $word1" # one
  echo "word2: $word2" # two
  echo "word3: $word3" # three four
done
```

### `exec` command

`exec` is a builtin command of the Bash shell that allows you to execute a command that completely replaces the current process. The current shell process is destroyed, and entirely replaced by the command you specify.

The `fork` system call makes a copy of the forking program and then uses `exec` to execute the child process in memory space.

#### Example 1:

Replace current bash sheen with rbash.

```
exec rbash
```

#### Example 2:

Redirect all output to the file `output.txt` for the current shell process. Redirections are a special case, and `exec` does not destroy the current shell process, but `bash` will no longer print output to the screen, writiing it to the file instead.

```
exec > output.txt
```