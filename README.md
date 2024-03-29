# Bash Template Command

*This `README.md` is autogenerated.*

This is a GitHub template repo that will be copied instead of forked to
create a new Bash command with a command something like this:

```
gh repo create rwxrob/mycmd -p rwxrob/template-bash-command
```

This `cmd` inside can then be renamed and finished.

## Naming Conventions

* Name repos containing single bash commands with `cmd-`
* Name template repos beginning with `template-`
* Start command functions with `x.` to be completed
* Name `CONF` accessors with `x.` and full path
* Use dot (`.`) pathing in `CONF` key names

Think of `x` as in "executable" command.

> ⚠️
> Note that all versions of Vim current have a bug that does not allow
> dot (`.`) to be included in the function name even though it is
> explicitly allowed by bash. So you may have to add `.` to your
> `/usr/share/vim/vim82/syntax/sh.vim` file anywhere a function name
> expression is defined. I have yet to isolate it out and override it in
> my own `.vimrc`. It is a minimal edit.

## Builtins and Utilities

A number of builtin and frequently used utility functions have been
included for convenience. These save developers from adding other
moronic things like `sed` and `awk` subprocesses. Obviously, not all of
this is needed for many Bash scripts. Just remove what you do not need
or want. 

### `_initialize`

The `_initialize` function is meant to contain initialization code and
be placed at the beginning of the script to be found easily even though
it is called at the bottom of the script (as bash requires). It is
passed the arguments that are passed to the script itself. This function can be omitted.

### `_alternatives`

The `_alternatives` function (usually placed after `_initialize`
provides a hook for dealing with alternative arguments to those that
identify commands (`x.*`). If the first argument to the script does not
match a command function then this function will be called before the
default `x.usage` command allowing shortcuts and other argument
alternatives and intelligent sensing of what command function is wanted
by simply examining the argument list. This can be useful when you wish
to provide shortcuts for longer commands but do not want to clutter the
command usage and completion list. For example, `zet dex.titles` could
be trapped in `_alternatives` to call `zet titles`. 

### `_have`

Returns true (0) if the first argument exists as an executable in the
current `PATH`. Otherwise, return false (1).

### `_checkdep`

Checks that the first argument exists as an executable in the current
`PATH`. If so, returns true (0). If not, prints a generic error message
in English and returns false (1).  The "progressive enhancement" design
principle requires minimal functionality using what is available and
progressively upgrading based on what is detected.

### `_newest`

Uses `ls` to return the newest file or directory in the specified
directory.

### `_trim`

Removes all whitespace (`[:space:]`) from the beginning and ending
of a string without invoking a subprocess.

### `_filter`

Reads the first argument or each line of standard input passing
each individually as the first argument to the calling function one at
a time. The UNIX philosophy requires all commands be filters whenever possible.

### `_buffer`

Reads the first argument or all lines of standard input and then
passes them to the calling function as the first argument. The UNIX philosophy requires all command be filters whenever possible.

### `_reduce`

Takes the name of an array and a bash extended regular expression
and prints only the array entries that match, one to a line suitable for
converting back into an array with `IFS=$'\n'` or just as an in-memory
`grep` replacement.

### `_jsonstr`

Encodes first argument or all standard input into a single line of JSON text. This function depends on the `jq` command.

### `_urlencode`

Encodes the first argument or all standard input using standard URL
encoding suitable for passing to `curl` or whatever. This function has
no external dependencies.

## Dependencies

Required:

* Bash 4+

Optional:

* `pandoc` - for rich help docs
* `jq` - for `json` and anything that uses it

## Justification

Bash is the dominant shell scripting language and the official default
Linux interactive shell, which reduces cognitive overhead; every command
line *is* a line of code that could be put into script as is. Bash
scripts are at the core of cloud, containers, and Kubernetes. Bash 4+
with its associative array support, powerful regular expressions, and
multiple ways of feeding data to loops easily covers the needs
previously requiring Python and Perl scripts. Bash scripts are also much
more powerful, safer, flexible, and performant than POSIX shell or Zsh.

## Guidelines

* Write GitHub Flavored Markdown only
* Use present tense ("outputs" over "will output")
* Prefer term "output" and "display" over ~~print~~
* Follow the [naming conventions](#naming-conventions) 
* Use the official bash path: `#!/bin/bash`
* Use of `#!/usr/bin/bash` is outdated
* Using `#!/usr/bin/env bash` introduces unnecessary risk
* Explicitly export `PATH` in script when possible
* Always check script with [`shellcheck`] before releasing
* Always use `bc` for *any* floating point math

[`shellcheck`]: <https://www.shellcheck.net>

## Legal

Copyright 2021 Rob Muhlestein <rob@rwx.gg>  
Released under Apache-2.0 License  
Please mention rwxrob.tv

## The `bar` Command

```
cmd bar
```

Bar the things.

## The `config` Command

```
cmd config
cmd config KEY
cmd config KEY VALUE
cmd config KEY ""
cmd config keys
cmd config val[ues]
cmd config dir[ectory]
cmd config path [file]
cmd config edit [file]
cmd config del[ete]
```

The `config` command is for reading, writing, and displaying standard
open desktop configuration properties. Pass an empty string to delete
a property.

### Arguments

With no arguments outputs all the currently cached configuration
settings.

With a single KEY argument fetches the value for that key and outputs
it unless it is one of the following special (reserved) key names:

* `dir*` full path to config directory
* `path` full path to specific config file (default: `values`) 
* `edit` opens config file in editor (default: `editor` or `$EDITOR)
* `keys` output the configuration keys, one per line
* `val*` output the configuration values, one per line
* `del*` if key argument then delete a specific key, otherwise prompt

With more than one argument the remaining arguments after the KEY will
be combined into the VALUE and written to a `values` file in the
configuration directory. 

### Configuration Directory

The configuration directory path relies on the following environment
variables:

* `EXE` - defaults to name of currently running command (cmd)
* `HOME` - checked for `$HOME/.config/$EXE/values`
* `XDG_CONFIG_HOME` - overrides `$HOME/.config`
* `CONFIG_DIR` - full path to directory containing `values` file

The `CONFIG_DIR` always takes priority over anything else if set, but is
never implied. If the directory does not exist it will be created the
first time a value is set.

### Configuration `values` File Format

The file (which is almost always located at
`~/.config/cmd/values`) uses the simplest possible format to
facilitate standard UNIX parsing and filtering with any number of
existing tools (and no `jq` dependency).

* One KEY=VALUE per line
* KEYs may be anything but the equal sign (`=`)
* VALUEs may be anything but line returns must be escaped

Note that this is *not* the same as Java properties and other similar
format. It is designed for ultimate simplicity, efficiency, and
portability.

## The `foo` Command

Foos things.

## The `help` Command

```
cmd help [COMMAND]
```

Displays specific help information. If no argument is passed displays
general help information (main). Otherwise, the documentation for the
specific argument keyword is displayed, which usually corresponds to
a COMMAND name (but not necessarily). All documentation is written in
GitHub Flavored Markdown and will displayed as a web page if `pandoc`
and `$HELP_BROWSER` are detected, otherwise, just the Markdown is sent
to `$PAGER` (default: more).

Also see `readme` and `usage` commands.

## Generate `README.md` File

```
cmd readme > README.md
```

The `readme` command will output the embedded help documentation in raw
GitHub Flavored Markdown suitable for use as a `README.md` file on
GitHub or similar hosting service.

## The `some.config.setting` Command

Get and set `some.config.setting`.

## The `usage` Command

Displays a summary of usage.

----

*Autogenerated Wed Sep  8 10:06:20 AM EDT 2021*

