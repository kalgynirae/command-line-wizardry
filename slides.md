# Command-Line Wizardry

## The command-line programs and features discussed here...

* Started with the Unix operating system
* Many modern operating systems are Unix-like, including
    * Linux
    * OS X
* Windows isn't Unix-like, but you can
    * connect remotely to another computer
    * use Cygwin

https://upload.wikimedia.org/wikipedia/commons/7/77/Unix_history-simple.svg

# Overview

## Basic components of the command line

* terminal (gnome-terminal, iTerm2, Cygwin Terminal, …)
    * displays text to you
    * handles your keypresses (and possibly mouse clicks)
* shell (Bash, Z shell, …)
    * displays the prompt (e.g., `colin@lumeh:~ $`)
        * typically represented by `$` in examples
    * interprets the commands you type
    * runs other programs
* programs
    * controlled by command-line options/flags
    * follow the Unix philosophy

## The Unix philosophy

> This is the Unix philosophy: Write programs that do one thing and do it well.
> Write programs to work together. Write programs to handle text streams,
> because that is a universal interface.
>
> — Doug McIlroy (inventor of Unix pipes)

## Command-line programs

* start with three open file descriptors
    * `stdin` – for reading input
    * `stdout` – for writing output
    * `stderr` – for writing error messages
* produce an exit status
    * integer, 0 to 255
    * 0 means success
    * meaning of other numbers differs per program

## File redirection

* `stdin`, `stdout`, and `stderr` are usually connected to the terminal
* can be directed to a file instead
    * `>` (or `1>`) redirects `stdout`
    * `2>` redirects `stderr`
    * `<` redirects `stdin`

    $ cat file1 file2 >combined
    $ sort <data >sorted_data
    $ grep 2>error.txt

## Chaining programs

* programs can be chained together using a *pipe*

    $ ls | grep md

# Exit Codes
