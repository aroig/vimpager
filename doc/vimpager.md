% VIMPAGER(1) vimpager user manual
% Rafael Kitover <rkitover@gmail.com>
% February 6, 2015

# NAME

vimpager - pager using vim and less.vim

# SYNOPSIS

vimpager [options] 'some file'

&#35; or (this won't always syntax highlight as well)

cat 'some file' | vimpager [options]

For vimcat see [here](doc/vimcat.md) or 'man vimcat'.

# RUN-TIME DEPENDENCIES

* vim

# BUILD DEPENDENCIES

* sharutils or some uuencode (only if you change the src/*.vim sources)
* pandoc (for man pages and html, optional)

# INSTALL

On Ubuntu or Debian, use the following to install a package:

```bash
git clone git://github.com/rkitover/vimpager
cd vimpager
sudo make install-deb
```

Otherwise use 'make install' instead:

```bash
git clone git://github.com/rkitover/vimpager
cd vimpager
sudo make install
```

If you got vimpager from the vim.org scripts section, just put it
somewhere in your PATH, e.g.:

```bash
cp vimpager ~/bin
chmod +x ~/bin/vimpager
```

In your ~/.bashrc add the following:

```bash
export PAGER=/usr/local/bin/vimpager
alias less=$PAGER
alias zless=$PAGER
```

# DESCRIPTION
A PAGER using less.vim with support for highlighting of man pages and
many other features. Works on most UNIX-like systems as well as Cygwin
and MSYS.

On GitHub: <http://github.com/rkitover/vimpager>

To use a different vimrc with vimpager, put your settings into a ~/.vimpagerrc
or ~/.vim/vimpagerrc or a file pointed to by the VIMPAGER_RC environment
variable.

You can also have a global config file for all users in /etc/vimpagerrc, users
can override it by creating a ~/.vimpagerrc or a ~/.vim/vimpagerrc.

These are the keys for paging while in vimpager, they are the same as in
less for the most part:

| _Key_   | _Action_                | _Key_   | _Action_                  |  
|---------|-------------------------|---------|---------------------------|  
|Space    |One page forward         |b        |One page backward          |  
|d        |Half a page forward      |u        |Half a page backward       |  
|Enter    |One line forward         |k        |One line backward          |  
|G        |End of file              |g        |Start of file              |  
|N%       |percentage in file       |,h       |Display this help          |  
|/pattern |Search forward           |?pattern |Search backward            |  
|n        |next match               |N        |Previous match             |  
|ESC-u    |toggle search highlight  |         |                           |  
|q        |Quit                     |,v       |Toggle Less Mode           |  

To disable loading plugins, put "set noloadplugins" into a vimpagerrc
file.

You can also switch on `exists('g:vimpager.enabled')` in your vimrc to set
alternate settings for vimpager.

**WARNING:** Option names have changed from the previous releases to use a
dict, if you use the old option names and check on `exists('g:vimpager')`
everything will work the same way, if you use the new option names you must
check `exists('g:vimpager.enabled')` instead.

**NOTE:** Before setting the vimpager and less.vim related options described
below, make sure the `g:vimpager` and `g:less` dicts exist like so:

```vim
let g:vimpager = {}
let g:less     = {}
```

If you want to disable less compatibility mode, and use regular vim
motion commands, put this into your .vimrc/vimpagerrc:

```vim
let g:less.enabled = 0
```

You can still enable less mode with this setting by pressing ",v".

At runtime whenever less mode is toggled with ,v , this variable will
reflect the current state, regardless of the initial value.

Put the following into your .vimrc/vimpagerrc if you want to use gvim/MacVim
for your pager window:

```vim
let g:vimpager.gvim = 1
```

To turn off the feature of passing through text that is smaller than the
terminal height use this:

```vim
let g:vimpager.passthrough = 0
```

See "PASSTHROUGH MODE" further down.

To turn on line numbers set:

```vim
let g:less.number = 1
```

they are turned off by default. You can also invoke vimpager with the `-N`
option to turn on line numbers.

To start vim with -X (no x11 connection, a bit faster startup) put the following
into your .vimrc/vimpagerrc:

```vim
let g:vimpager.X11 = 0
```

**NOTE:** this may disable clipboard integration in X terminals.

The scroll offset (:help scrolloff), may be specified by placing the 
following into your .vimrc/vimpagerrc (default = 5, disable = 0):

```vim
let g:less.scrolloff = 5
```

The default is 5 only in less mode, with less mode disabled the default
is the user's scrolloff setting.

The process tree of vimpager is available in `vimpager.ptree`, an example usage
is as follows:

```vim
if exists('g:vimpager.enabled')
  if exists('g:vimpager.ptree') && g:vimpager.ptree[-2] == 'wman'
    set ft=man
  endif
endif
```

To disable the use of AnsiEsc.vim to display ANSI colors in the source,
set:

```vim
let g:vimpager.ansiesc = 0
```

see the section "ANSI ESCAPE SEQUENCES AND OVERSTRIKES" for more
details.

# COMMAND LINE OPTIONS

## + | +G

Start at the end of the file, just like less.

## -N | --LINE-NUMBERS

Turn on line numbers, this can also be set with `let g:less.number = 1` .

## -c cmd

Run a vim command after opening the file. Multiple -c arguments are
supported.

## --cmd cmd

Run a vim command when entering vim before anything else. Multiple --cmd
arguments are supported.

## -u vimrc

Use alternate .vimrc or .vimpagerrc.

## -s

Squeeze blank lines into a single blank line. GNU man passes this option to
/usr/bin/pager.

# ANSI ESCAPE SEQUENCES AND OVERSTRIKES

If your source is using ANSI escape codes, the AnsiEsc plugin will be
used to show them, rather than the normal vim highlighting, however read
the caveats below. If this is not possible, they will be stripped out
and normal vim highlighting will be used instead.

Overstrikes such as in man pages will always be removed.

vimpager bundles the
[AnsiEsc](http://www.vim.org/scripts/script.php?script_id=4979)
plugin (it is expanded at runtime,
there is nothing you have to do to enable it.)

However, your vim must have been compiled with the 'conceal' feature
enabled. To check, try

```vim
:echo has('conceal')
```

if the result is '1' you have conceal, if it's '0' you do not, and the
AnsiEsc plugin will not be enabled.

If you're on a Mac, the system vim does not enable this feature, install
vim from Homebrew.

To disable the use of AnsiEsc.vim, set:

```vim
let g:vimpager.ansiesc = 0
```

If the file has a modeline that sets ft or syntax, the setting will override
the use of AnsiEsc.

To turn off AnsiEsc while viewing a file, simply run

```vim
:AnsiEsc
```

To turn off AnsiEsc on the commandline, use an invocation such as the following:

```sh
vimpager -c 'au VimEnter * exe "setlocal syntax=".&syntax' somefile
```

**NOTE:** The `conceal` feature of vim is still very buggy, especially as
concerns spacing, and the line wrapping in files highlighted with `AnsiEsc`
will not be correct (they are wrapped too soon.) The tab stops will be correct
however, this is fixed up with a vim script.

**NOTE:** `AnsiEsc` is a work in progress, and will only display files with
simple ANSI codes correctly, such as that output by git tools. More complex
highlighting is likely not going to work right now. We are working on this.

# PASSTHROUGH MODE

If the text sent to the pager is smaller than the terminal window, then
it will be displayed without vim as text. If it has ansi codes, they
will be preserved, otherwise the text will be highlighted with vimcat.

You can turn this off by using:

```vim
let g:vimpager.passthrough = 0
```

Passthrough mode requires a POSIX shell with arithmetic expansion, if
there is one on your system and it is not detected please submit an
issue with the path and your OS version.

# CYGWIN/MSYS/MSYS2 NOTES

vimpager works correctly with the native Windows gvim, just put it in
your PATH and set the vimpager_use_gvim option as described above.
