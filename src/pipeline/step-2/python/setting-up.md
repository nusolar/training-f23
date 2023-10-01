# Setting up

In this section, you'll be guided through a Python installation and
development setup.

> The instructions in the section are opinionated. That is, there are
> alternative ways to orchestrate a Python development environment,
> but the author of this handbook recommends the ways listed here.
> If you are familiar with and prefer an alternative method (e.g.
> anaconda), feel free to diverge from these instructions, but it
> may become harder to debug your specific setup if the need arises.

## macOS
If macOS is your operating system of choice, you likely already have
Python installed, but the specific version is probably old and best left
alone. To get started, open the terminal (Terminal.app in Applications).
The terminal is an environment where you can control your computer with
textual commands instead of a GUI. You will use the terminal a lot in
your software career.

> In the terminal, commands are issued following the prompt (typically a
> `$` or `>`). Commands listed here follow the format `$ [command]`, to
> indicate the `[command]` is to be run in the terminal. When copying
> these commands, do not copy the leading `$`.

### Install XCode command line tools
First, you should install a suite of command line tools Apple bundles
for developers, if you have not already. To do this, run:

```bash
$ xcode-select --install
```

### Install Homebrew
[Homebrew](https://brew.sh/) is a package manager for macOS. Package
managers allow you to easily install and organize software packages. We'll
need Homebrew to install some packages later. If you don't already have
Homebrew, you can install it with:

```bash
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

With that done, run:

```bash
$ brew doctor
```

to ensure the everything is installed correctly, and then run:

```bash
$ brew update
```

to ensure things are up-to-date.

### Install pyenv
We're going to use [pyenv](https://github.com/pyenv/pyenv) to manage our Python
installation. While you can install Python directly through Homebrew, the version
may be a couple months out of date. Pyenv allows you to manage multiple Python
versions easily, and you will never have to wait for Homebrew if you need to update
to the latest version.

```bash
$ brew install pyenv
```

Next you will need to follow the
[shell setup for pyenv](https://github.com/pyenv/pyenv#set-up-your-shell-environment-for-pyenv)
to properly integrate pyenv into the command line. To check which shell you are using (likely
zsh, but possibly bash), run:

```bash
$ echo $SHELL
```

Once you have followed the shell setup instructions, restart your shell:

```bash
$ exec "$SHELL"
```

Now you should be able to use pyenv to manage Python versions. To start, run:

```bash
$ pyenv versions
```

You should see that only the system python is installed. To install the latest version
of Python (3.11.5 currently), run:

```bash
$ pyenv install 3.11.5
```

Then, you may want to set this version to be the global default:

```bash
$ pyenv global 3.11.5
```

### Install direnv
You should now have a new global installation of Python. At this point, you could
begin writing Python scripts and installing Python packages. For various reasons,
however, it is not advisable to install packages in your global Python environment.
Instead, best practices advise creating per-project environments so as to avoid
any cross-project conflicts. There are *many* ways to create and manage so-called
virtual environments (virtualenv, venv, pipenv, anaconda, poetry, direnv, pyenv-virtualenv,
etc.), but the author recommends [direnv](https://direnv.net/).

Direnv allows you to create project environments that automatically load/unload
when you enter/exit the project folder. Install it with Homebrew:

```bash
$ brew install direnv
```

To work properly, direnv need to be able to hook into the shell. Follow the
[setup instructions](https://direnv.net/docs/hook.html) to finish the installation.
For example, if you are using zsh, you would run:
```zsh
$ echo 'eval "$(direnv hook zsh)"' >> ~/.zshrc
```

> If you use [tmux](https://github.com/tmux/tmux/wiki) as a terminal multiplexer,
> you may need to make some adjustments to your shell config file to ensure everything
> works. For example, the author of this handbook had to add the following lines to
> his `.zshrc`:
>
> ```bash
> # Make tmux play nicely with direnv
> alias tmux='direnv exec / tmux'
> 
> # Need to reload direnv in tmux for some reason to ensure
> # correct environment variables. Particularly for pyenv.
> if [ -n "$TMUX" ] && [ -n "$DIRENV_DIR" ]; then
>     export DIRENV_LOG_FORMAT= # Don't print this reload or the Powerlevel10k prompt will freak out.
>     direnv reload
> fi
> ```

## Windows
Windows is a trickier platform for Python development. Because many packages are
only available on Unix-like platforms (macOS or Linux), and because the Raspberry
Pi runs Linux anyway, it's best for consistency to install the Windows Subsystem
for Linux (WSL). To install WSL, follow [Microsoft's instructions for setting up
a development environment](https://learn.microsoft.com/en-us/windows/wsl/setup/environment)
(follow up through the "Update and upgrade packages" section; the following sections
are worth reading but not necessary right now).

Once you have setup WSL and are in a Linux command line, follow the previous instructions
for installing pyenv and direnv, but use your system package (`apt` if on Ubuntu) instead of
Homebrew.

## Text Editor
To actually write code, you will need a text editor or an IDE. This is the subject of a
long-running programming flame war, but [VS Code](https://code.visualstudio.com/) and
[PyCharm](https://www.jetbrains.com/pycharm/) are good choices.
