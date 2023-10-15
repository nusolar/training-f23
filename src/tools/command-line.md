# The command line

> Commands given in this chapter follow the format `$ [command]` first
> mentioned in the [Python setup chapter](../pipeline/step-2/python/setting-up.md).
> Remember that the `$` indicates that the `[command]` should be run in the
> terminal and is not itself part of the command.
>
> This chapter also assumes a Unix (i.e. macOS or Linux) command line.

Computers are most often used through the graphical user interface (GUI) provided
by the operating system. GUIs make common activities like browsing the web, reading
a PDF, etc. easy for humans. Programs, however, often prefer to operate on text
accessed through "the command line". The command line is a place where you use
a program through its command line interface (CLI) as opposed to a GUI. Familiarity
with the command line is very useful for a programmer, and while this chapter will
only touch briefly on the basics of the terminal, it will hopefully help you
understand the command line usage throughout this handbook.

## Navigating file hierarchies

You are probably already familiar with using your operating system's GUI for file
management (Finder on macOS, File Explorer on Windows) to create/modify/delete folders
(also called "directories") and files. You can do the same from the command line.

To begin, open a terminal and run:

```bash
$ pwd
```

The `pwd` command prints the current/working directory. The output you see may look
like `/Users/username`. This is a Unix file path. File paths are a way to identify
a file or directory by constructing a "path" to follow in order to reach it from the
universal top-most directory (called the "root" directory). For example, consider the
following path:

```bash
/Users/admin/documents/report.pdf
```

This path says:
* Inside of the root (`/`) folder, there is a folder called `Users`.
* Inside of the `Users` folder, there is a folder called `admin`.
* Inside of the `admin` folder, there is a folder called `documents`.
* Inside of the `documents` folder, there is a file called `report.pdf`.

Notice that folders on Unix-like systems are separated by `/`. On Windows, the
path separator is `\\`. Also notice that files (typically) contain an extension
that hints at the file type. The file extension for `report.pdf` is `.pdf`, meaning
this file is likely a PDF.

Now that you understand Unix paths, let's discuss how to list files and move between folders.
For the remainder of this section, we'll assume the following file hierarchy, although you
are encouraged to try listing files on your own computer:

```
foo
├── bar
│   ├── images
│   │   ├── cat.png
│   │   └── dog.png
│   └── info.txt
└── baz
    └── hello.txt
```

Let's assume you are currently positioned in the `foo` folder. The `ls` command lists files
in the current directory:

```bash
$ ls
bar baz
```

You can also give `ls` a path to a folder.

```bash
$ ls ./bar/images
cat.png dog.png
```

Notice the use a relative path. Instead of specifying the absolute path starting at the root
(`/`), we gave a path relative to the current directory (`.`). On Unix-like systems, `.` means
current directory and `..` means parent directory.

We can change our current directory using `cd`:

```bash
$ cd ./bar
$ ls
images info.txt
```

Now, we've set the current folder to `foo/bar`. If wanted to move into `foo/baz`, we can
move back into the parent directory and then into `baz`:

```bash
$ cd ..
$ cd ./baz
```

Note that we could have equivalently used `cd` just once by writing `$ cd ../baz`.

We can use the `cat` command to print the contents of a file.
```bash
$ cat hello.txt
Hello!
$ cat ../bar/info.txt
This is some important info.
```

Using `ls`, `cd`, and `cat` is the simplest way to navigate through a file system on the
command line. On their own, they aren't very powerful, but they will come in handy any
time you find yourself on the command line. There is, of course, so much more to learn
about the command line that we can't cover here. If you're interested, check out
[this tutorial](http://www.ee.surrey.ac.uk/Teaching/Unix/).

## Remote development
You will often end up reaching for the command line when developing on remote computers.
For example, our Raspberry Pi microprocessor does not come with a screen or a keyboard.
And although we have purchased ones, it's often easier to access the Pi through the terminal.

### SSH
The Secure Shell Protocol (SSH) is a way to access remote computers through a CLI. You
probably already have SSH installed (OpenSSH, specifically). If not, you can install it
through your package manager.

In this section, we'll log in to the Raspberry Pi over SSH. Because this involves sharing
the IP address and password of our Pi, we will cover this in person, instead of writing it
here.
