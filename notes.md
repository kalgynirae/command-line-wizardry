# Command-Line Wizardry

> “Any sufficiently advanced command-line user is indistinguishable from a
> wizard.”
>
> — Chan's 3rd law

## This document...

* is in general about the command-line environment on [Unix-like
  systems][unix-history], especially
    * Linux
    * OS X
* Windows isn't Unix-like, but you can
    * [use Cygwin][cygwin]

## Recommendations for installing software

* Linux: use your system's package manager
* OS X: use [Homebrew][homebrew]
* Windows: [use Cygwin][cygwin]

### What is a package manager?

> A **package manager** or **package management system** is a collection of
> software tools that automates the process of installing, upgrading,
> configuring, and removing software packages for a computer's operating system
> in a consistent manner. It typically maintains a database of software
> dependencies and version information to prevent software mismatches and
> missing prerequisites.
>
> — Wikipedia

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
* often have a "man page" or a help option
    * `man wc`
    * `tr --help`
    * `git add -h`

# Shell basics

Disclaimer: I use **Bash** because it is the default in most Linux
distributions. All of the examples work in Bash, and the majority of them should
work in **sh** as well. However, the concepts are general enough that you should
be able to adapt them to whatever shell you choose.

## Important keyboard shortcuts

* **Ctrl+c** (`^C`): terminate the currently running program
* **Up**: recall previously-entered commands

## Shell scripts

* A shell script is just a file where each line is a command.
* Executing a shell script is no different than typing the lines of the script
  one at a time at the shell prompt.

## Chaining commands (pipes)

* Programs can be chained together using a *pipe*
    * Connects the first program's `stdout` to the second program's `stdin`

```bash
$ ls -tr | tail -1                          # most-recently-modified file
$ ps -e | grep auda                         # is Audacity running?
$ sort <stuff | uniq                        # print unique lines from stuff
$ tr -cs A-Za-z '\n' <file | tr A-Z a-z | \
  | sort | uniq -c | sort -rn | sed ${1}q   # print most common words w/ freq.
```

## Multiple commands

* Commands can be sequenced in several ways
    * `foo; bar`: Execute `foo` then `bar`.
    * `foo && bar`: Execute `foo`; if `foo` is successful (exit status == 0),
      then execute `bar`.
    * `foo || bar`: Execute `foo`; if `foo` is *not* successful then execute
      `bar`.
    * In all cases, the exit status of the sequence is the exit status of the
      last-executed command.

## Running commands in the background

* `&` at the end of a command runs it in the background
* `jobs`: list currently running/suspended commands
* **Ctrl+z**: suspend a command running in the foreground
* `bg`: start a suspended command running in the background
* `fg`: start a suspended command running in the foreground

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

## Arithmetic expressions

* Use `(( ... ))` or `$(( ... ))` to evaluate arithmetic expressions
    * `if (( $x < 5 )); ...`
    * `result=$(( $base ** 2 + 0xa0 ))`
    * `echo $(( 0xa0 ))

## Conditional expressions

* Old way (might be needed for compatibility: `[ ... ]` or `test ...`
    * Be careful to quote properly!
* Bash way: `[[ ... ]]`
    * Quoting is not necessary.
    * See **CONDITIONAL EXPRESSIONS** in the Bash `man` page.

## If

* General format: `if command; then stuff; fi`
* If `command` exits successfully, then `stuff` is executed.

```bash
if [[ -r $file ]]; then
  wc -l <"$file"
fi
```

```bash
file=/tmp/stuff.txt
if grep ^foo "$file"; then
  echo "$file contains a line that starts with 'foo'"
else
  echo "$file does not contain a line that starts with 'foo'"
fi
```

## Loops

```bash
for i in *.mp3; do
  echo ffmpeg -i "$i" "${i%mp3}ogg"
done

while true; do
  echo still going...
  sleep 1
done
```

## Command substitution

* Some programs refuse to read from stdin.
...

## Source of truth

```bash
$ man bash
```

* The [Bash Guide][bash-guide] is also very good.

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

* simple replacements

```bash
$ alias myip='dig +short myip.opendns.com @resolver1.opendns.com'
$ myip
68.101.68.150
$ alias ll='ls -lh'
$ alias grep='grep --color'
```

## Functions

* more complex than aliases

```bash
mkcd() {
    mkdir -p "$1"
    cd "$1"
}

git() {
    if command git rev-parse --is-inside-work-tree &>/dev/null; then
        command git "$@"
    else
        echo "Not a git repo. You probably meant:"
        hg githelp -- git "$@"
    fi
}
```

## Environment variables

* `EDITOR`: default text editor (used by things like `git commit`)
* `LESS`: default options for `less`
* `HISTSIZE`: how many past commands Bash should save
* `PS1`: prompt, as mentioned earlier

## Shell options

* `set -o noclobber`: Tells Bash to refuse to overwrite files with redirection
    * can be overridden using the special redirection operator `>|`
* `bind '"\e[A":history-search-backward'`: Make up-arrow recall commands that
  prefix match with what you've typed so far
* `bind '"\e[B":history-search-forward'`: likewise for down-arrow

# Useful programs

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
* `tr`: Translate/delete characters
* `sort`: Sort input lines
* `uniq`: Report or omit repeated lines
* `cat`: Concatenate files (watch out for [Useless Use of Cat][uuoc])
* `wc`: Count words/lines/characters
* `sed`: Perform regex substitutions on each line (and more)
* `awk`: More complicated than `sed`

## Files

* `mv`: Move/rename files/directories
* `cp`: Copy files/directories
* `mkdir`/`rmdir`: Create/remove directories
* `tar`: Create/extract archives (optionally compressed)
* `rsync`: Super-advanced `cp` with all the options you could possibly imagine
* `find`: Find files/directories and run commands on them
* `tree`: Print a nice ASCII filesystem tree

## Editors

* `nano`: Beginner-friendly text editor
* `vim`: Non-beginner-friendly text editor
* `emacs`: Text editor for people who like Lisp

## Network

* `ping`: Test connectivity
* `ssh`: Securely connect/tunnel to remote computers
* `scp`: Copy files via ssh
* `curl`: Download things and make HTTP requests
* `dig`: Make DNS queries

## Other

* `youtube-dl`: Download videos from YouTube
* `mplayer`: Audio/video player
* ImageMagick (`convert`): Convert/manipulate images

# Nifty examples

* Rename spaces to underscores: `for i in *; do mv "$i" ${i// /_}; done`
    * Safer: `for i in ./*' '*; do mv "$i" "${i// /_}"; done`
* Most-recently-modified file: `ls -tr | tail -1`
* Get public IP address: `dig +short myip.opendns.com @resolver1.opendns.com`
* Convert `.mp3` files to `.ogg`: `find -name *.mp3 -exec sox {} {}.ogg \;`
* Tweet: `curl -u username -d status='Hello World, Twitter!' -d source="cURL"
  http://twitter.com/statuses/update.xml`
* Stopwatch: `time read`
* Star Wars: `telnet towel.blinkenlights.nl`
* [Download file with retry and text message confirmation][download-retry]
* [Clean up whiteboard photos with ImageMagick][whiteboard]
* [Share your cool Bash One-Liners ?][bash-oneliners]

# Wizardly tips

## SSH

...

## Common command-line options

* `-` in place of a filename means read from `stdin` or write to `stdout`
* `--` means no options follow (might be needed if you have a filename that
  starts with a hyphen)

## Navigation

* `pushd`: like `cd` but puts the old directory onto a stack so you can `popd`
  to get back.

[unix-history]: https://upload.wikimedia.org/wikipedia/commons/7/77/Unix_history-simple.svg
[cygwin]: https://imgur.com/a/6hbpR
[homebrew]: http://brew.sh/
[bash-guide]: http://mywiki.wooledge.org/BashGuide
[uuoc]: https://en.wikipedia.org/wiki/Cat_%28Unix%29#Useless_use_of_cat
[download-retry]: https://gist.github.com/kalgynirae/50389c3470eebd5a2de7
[whiteboard]:https://gist.github.com/lelandbatey/8677901
[bash-oneliners]: https://www.reddit.com/r/linuxadmin/comments/2lvhn7/share_your_cool_bash_oneliners/
