# bash

[Bash Reference Manual](https://www.gnu.org/software/bash/manual/html_node/index.html#SEC_Contents)

## Modify the Behaviour of the Shell

You can set shell options at the top of a script file in order to modify the behaviour the shell. See [here](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html) for a comprehensive list of options, but some of the most common/useful are as follows:

```bash
set -u # Causes unset variables to trigger an error, instead of being treated as just an empty variable
set -e # Exit if any command results in an error or non-zero return code
set -o pipefail # Fail an entire pipeline if a single command fails
```

## Recommended Start of All Scripts

```bash
#! /bin/bash

set -eu
set -o pipefail

# Save global script args
ARGS=("$@")

```

## Bash Builtins

#### **test**

`test` returns an exit status of 0 for true and 1 for false. Can be used to quickly test expressions, and combined with set -e can exit out of scripts upon critical failures.

[Reference](https://www.computerhope.com/unix/bash/test.htm)

## Useful Utilities

#### **xargs**

A command line utility for building execution pipelines from standard input. It allows tools like `echo`, `rm` and `mkdir` to accept standard input as arguments.

```bash
# Executs 'mkdir' three times for each input argument, creating folders 'dir1', 'dir2', and 'dir3'
echo 'dir1 dir2 dir3' | xargs mkdir
```

It is often paired with the `find` command in order to batch process files.

#### **find**

As it's name implied, `find` helps search files and directories to find those that match a particular criteria (name, file type, file size etc).

`TODO: Add examples`

#### **awk**

General-purpose scripting language designed for advanced text processing. It is mostly used as a reporting and analysis tool.

`TODO: Add examples`

#### **grep**

Stands for 'global regular expression print'. It can be used to search for lines that contain information that matches a particular pattern/expression in a file or from standard input (e.g. the output of another command).

`TODO: Add examples`

#### **sed**

A stream editor that can perform basic text manipulation (e.g. fine, replace, insert, delete words and lines). It supports basic and extended regular expressions that allow you to match complex patterns.

`TODO: Add examples`

#### **sort**

Sorts the contents of a text file. The sorting type and direction can be modified using options.

`TODO: Add examples`

#### **wc**

Counts the number of lines in a file/standard input.
