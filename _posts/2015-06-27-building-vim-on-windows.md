---
layout: post
title:  Building Vim and gVim on Windows
author: Michael Giuffrida
#date:   2015-06-27 16:28:00
---

> Build Vim from source natively on Windows 7, Windows 8.1, and ~~Windows 9~~
> Windows 10

Realizing my version of Vim on Windows was quite stale, I decided to build it
myself from source. On Windows 8.1. How hard could it be?

Well, it's actually pretty easy once you have the right tools. I kept running
into out-of-date documentation, so I'll try to keep this future-compatible by
explaining what you need for a frustration-free experience.

## Get the tools

You'll want to grab a recent edition of [Visual Studio][vs] for its compilers.
I'd recommend getting a recent free edition like the [Express for Windows
Desktop or Community editions][vs-editions].

You'll also need the Windows SDKs as they don't ship with Visual Studio anymore.
The SDKs provide header files we need to compile.

First, download the [Windows 7 SDK][sdk-7], even if you're on Windows 8.1,
because it has a file we need. If you're using Windows 8.1, you can download the
[Windows 8.1 SDK][sdk-8.1] as well if you like. In any case, only install the
Windows SDK proper; you don't need to install the other stuff, like the .NET SDK
or the Performance Toolkit.

[vs]: https://www.visualstudio.com
[vs-editions]: https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
[sdk-7]: https://www.microsoft.com/en-us/download/details.aspx?id=3138
[sdk-8.1]: https://msdn.microsoft.com/en-us/windows/desktop/bg162891.aspx

## Get the source

[The Vim source][vim-source] uses Mercurial, a distributed version control
system much like Git. [Grab Mercurial if you don't have it][mercurial] -- you
can find Git/SVN mirrors, but they lag behind the trunk. Get the source with:

    hg clone https://code.google.com/p/vim/

Downloading and applying the changes will take a couple minutes.

[vim-source]: https://code.notgoogle.com/p/vim/source/checkout
[mercurial]: https://mercurial.selenic.com/downloads

## Configure the build

Let's make a batch file alongside the `vim` directory called `configure.cmd` (or
[download here][configure.cmd]):

{% highlight bat %}
{% include code/building-vim-on-windows/configure.cmd %}
{% endhighlight %}

> If you're using the Windows 8.1 SDK, you'll also need Win32.Mak, an options
> file for nmake. This file ships with the Windows 7 SDK, so you can simply
> copy it from 7.0\Include\Win32.Mak to 8.1\Include\WIn32.Mak.

Update the directories if necessary, and [set your desired
features][vim-features].  You can find more options in
[vim/src/Make_mvc.mak][Make_mvc.mak]. In addition to Python, you can link with
Python3 (but not both), Ruby, Perl, and more.

`CPU` specifies your target architecture, and `TOOLCHAIN` specifies how to
compile:

* For a 32-bit version: `CPU=I386` and `TOOLCHAIN=x86`
* For a 64-bit version: `CPU=AMD64` and `TOOLCHAIN=x86_amd64`

You can build either version, but the 64-bit version will only run on a 64-bit
machine. It'll be capable of handling multi-gigabyte files, which is nice, and
may run slightly faster. (On a 64-bit machine, you could also use the native
amd64 toolchain if your version of VS has it, but it really doesn't make
a difference.)

If you link to a third-party library like Python, be sure you have the appropriate version (32-bit or 64-bit).

[configure.cmd]: https://raw.githubusercontent.com/mgiuffrida/mgiuffrida.github.io/master/_includes/code/building-vim-on-windows/configure.cmd
[vim-features]: http://vimdoc.sourceforge.net/htmldoc/various.html#:version
[Make_mvc.mak]: https://code.google.com/p/vim/source/browse/src/Make_mvc.mak

## Build Vim!

Now create `build.cmd` alongside the `vim` directory (or [download
here][build.cmd]):

{% highlight bat %}
{% include code/building-vim-on-windows/build.cmd %}
{% endhighlight %}

Invoke `configure` once, then invoke `build` to compile vim.exe or gvim.exe,
depending on the value of `GUI`.

**Any time you change the values in** `configure.cmd` **and re-run**
`configure`**, run** `build clean` **before building again.**

[build.cmd]: https://raw.githubusercontent.com/mgiuffrida/mgiuffrida.github.io/master/_includes/code/building-vim-on-windows/build.cmd

## Install Vim

First, you need to copy the executables, docs, etc. to a directory named
`vim74`. Create a script named `copy-vim.cmd` (or [downolad
here][copy-vim.cmd]):

{% highlight bat %}
{% include code/building-vim-on-windows/copy-vim.cmd %}
{% endhighlight %}

`copy-vim` will place everything you need in vim74. You can stop here, if you'd
like.

`vim74\install.exe` will run a command-line "installer". Use the given numbers
to disable actions you don't want---I disabled 10 (create startup file), 14
(context menu), and 17--19 (desktop icons). Enter `d` to perform the actions,
and you should be all set.

If you want to install the batch files to launch Vim in the default location
(C:\windows), you'll need to run `install.exe` as an administrator. You can open
the Command Prompt from the Start menu as an administrator by holding
Ctrl+Shift, or just right-click `install.exe` and choose "Run as administrator".
You could also install to somewhere else in your `PATH`.

[copy-vim.cmd]: https://raw.githubusercontent.com/mgiuffrida/mgiuffrida.github.io/master/_includes/code/building-vim-on-windows/copy-vim.cmd

## Troubleshooting

#### configure.cmd

> The input line is too long. The syntax of the command is incorrect.

Visual Studio is kind of silly---it will repeatedly add stuff to your PATH until
it becomes too long to work. Just close and re-open the Command Prompt and run
`configure` from scratch.

> The specified configuration type is missing. The tools for the configuration
> might not be installed.

Your VS installation doesn't include the `TOOLCHAIN` you're trying to use, so
`vcvarsall.bat` is unable to find the correct `vcvars[toolchain].bat` file.
E.g., Visual Studio Express may only include x86 and x86_amd64 (to cross-compile
for AMD64 using the x86-amd64 toolchain). Try setting `TOOLCHAIN` to x86_amd64
if you want a 64-bit build, or x86 otherwise:

 Your CPU | %CPU% | %TOOLCHAIN% | Output
 --------:|:-----:|:------------|-------
 64-bit   | AMD64 | amd64       | 64-bit
 any      | AMD64 | x86_amd64   | 64-bit
 any      | I386  | x86         | 32-bit
 64-bit   | I386  | amd64_x86   | 32-bit

#### copy-vim.cmd

> Sharing violation

Did you close Vim before running vim-copy?

#### :help content missing, runtime files not loading

If `:help` works when running Vim or gVim from the src directory (vim\src) but
not from your vim74 location, be sure the contents of vim\runtime are being
copied to vim74 via `copy-vim.cmd`.
