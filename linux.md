# Linux

## Users and Permissions
#### List Users

```bash
# All users details
cat /etc/passwd

# Just the username
awk -F: '{ print $1}' /etc/passwd
```

#### See Groups User is in

```bash
# Your own groups
groups

# Another users groups
groups <username>
```

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