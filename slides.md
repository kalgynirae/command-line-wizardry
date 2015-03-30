# Command-Line Wizardry

## The command-line programs and features discussed here...

* [Started with the Unix operating system][unix-history]
* Many modern operating systems are Unix-like, including
    * Linux
    * OS X
* Windows isn't Unix-like, but you can
    * connect remotely to another computer
    * use [Cygwin][cygwin]

# Overview

## Basic components

* terminal (gnome-terminal, iTerm2, Cygwin Terminal, …)
    * displays text to you
    * handles your key presses (and possibly mouse clicks)
* shell (Bash, Z shell, tcsh, …)
    * displays the prompt (e.g., `colin@lumeh:~ $`)
        * typically represented by `$` in examples
    * interprets the commands you type
    * runs other programs
* programs
    * follow the Unix philosophy

## The Unix philosophy

> This is the Unix philosophy: Write programs that do one thing and do it well.
> Write programs to work together. Write programs to handle text streams,
> because that is a universal interface.
>
> — Doug McIlroy (inventor of Unix pipes)

## Command-line programs

* are controlled by command line options and arguments
    * arguments are whitespace-separated
* start with three open file descriptors
    * `stdin` – for reading input
    * `stdout` – for writing output
    * `stderr` – for writing error messages
* produce an exit status
    * integer, 0 to 255
    * 0 means success
    * meaning of other numbers differs per program

## Pipes

* programs can be chained together using a *pipe*
    * connects the first program's `stdout` to the second program's `stdin`

```bash
$ ls -tr | tail -1                          # most-recently-modified file
$ ps -e | grep auda                         # is Audacity running?
$ sort <stuff | uniq                        # print unique lines from stuff
$ tr -cs A-Za-z '\n' <file | tr A-Z a-z | \
  | sort | uniq -c | sort -rn | sed ${1}q   # print most common words w/ freq.
```

## Redirection

* `stdin`, `stdout`, and `stderr` are usually connected to the terminal
* can be directed to a file instead
    * `>` (or `1>`) redirects `stdout`
    * `2>` redirects `stderr`
    * `<` redirects `stdin`

```bash
$ cat data{1,2,3,4,5}.txt >combined.txt
$ sort <data >sorted_data
```

## Quoting

* caution is required
* backslash is escape character
    * `mv horrible\ name sane_name`
    * or: `mv 'horrible name' sane_name`
* single quotes:
    * all characters are literal
* double quotes:
    * `$`, ````, `\\`, and `!` have special meaning
    * use when expanding variables that might contain special characters:
        * `$ python enhance-coolness.py "$infile"`

## Expansion

* braces: generate similar strings
    * `cat file_{foo,bar,egg} >combined`
    * `cat data{1..10} | wc -l`
* tilde: `~` at the beginning of a word expands to your home directory
    * `vim ~/.bashrc`
* parameter: variables, environment variables, command-line arguments
    * `echo $SHELL`
    * default value: `echo ${selection:-none}`
    * substring: `echo ${foo:0:5} ${foo:-3}` (first five, last three chars)
    * length: `echo ${#foo}`
    * remove suffix: `echo ${infile%.txt}.mp3`
    * pattern substitution: `mv "$foo" "${foo/TXT/txt}"`
* command substitution: replace command with its output
    * `echo The date is $(date).`
    * Can also use backticks, but they are less safe (can't nest, some
      characters are interpreted unexpectedly).
* glob (pathname): `*` `?` and `[...]` in paths
    * `echo *.txt`
    * `echo file?.txt`
    * `echo file[A-Z].txt`

# Customizing your shell environment

## Prompt

* printed before you type each command
* defined by the `PS1` environment variable

```bash
$ PS1='ok type here: -------> '
ok type here: -------> echo hi
hi
ok type here: -------> PS1='$'
$ echo that\'s better
that's better
```

## Aliases

```bash
$ alias myip='dig +short myip.opendns.com @resolver1.opendns.com'
$ myip
68.101.68.150
```

## Functions

# Useful programs

# Common Unix/Linux programs

## Shell builtins

* `cd`: Change directory
* `pwd`: Print working directory
* `alias`: Create/print aliases
* `echo`: Print arguments
* `type`: Find out whether a command is a built-in/function/executable

## Text manipulation

* `less`: View contents of a file or `stdin`
* `grep`: Print lines containing a regex match
* `head`/`tail`: Print the first/last lines of a file
* `sed`: Perform regex substitutions on each line (and more)
* `tr`: Translate/delete characters
* `sort`: Sort input lines
* `uniq`: Report or omit repeated lines
* `cat`: Concatenate files (watch out for [UUOC][uuoc])
* `wc`: Count words/lines/characters

## Files

* `mv`: Move/rename files/directories
* `cp`: Copy files/directories
* `mkdir`/`rmdir`: Create/remove directories
* `tar`:
* `rsync`:

## youtube-dl

[youtube-dl][youtube-dl]

## ImageMagick

* [clean up whiteboard photos][whiteboard]

# Examples

* Rename spaces to underscores: `for i in *; do mv "$i" ${i// /_}; done`
    * Safer: `for i in ./*' '*; do mv "$i" "${i// /_}"; done`
* Most-recently-modified file: `ls -tr | tail -1`
* Get public IP address: `dig +short myip.opendns.com @resolver1.opendns.com`
* Convert `.mp3` files to `.ogg`: `find -name *.mp3 -exec sox {} {}.ogg \;`
* [Share your cool Bash One-Liners ?][bash-oneliners]

[unix-history]: https://upload.wikimedia.org/wikipedia/commons/7/77/Unix_history-simple.svg
[cygwin]: https://imgur.com/a/6hbpR
[uuoc]: https://en.wikipedia.org/wiki/Cat_%28Unix%29#Useless_use_of_cat
[youtube-dl]: https://rg3.github.io/youtube-dl/
[imagemagick]: http://imagemagick.org/
[whiteboard]:https://gist.github.com/lelandbatey/8677901
[bash-oneliners]: https://www.reddit.com/r/linuxadmin/comments/2lvhn7/share_your_cool_bash_oneliners/
