# Step 0. Some History

***UNIX vs. BSD, System V vs. BSD, Linux vs. BSD, POSIX, “UNIX-like,”
Cygwin, and why any of this matters now. “Why does this script off the
internet work on this system and not on that one?”***

> *"That men do not learn very much from the lessons of history is the
> most important of all the lessons of history."* - Aldous Huxley

UNIX and its successors such as Linux have a long history reaching into the
depths of time:

* **Prehistory** - late 1960s, Nixon, Vietnam, Woodstock, Moon landing,
[Multics](https://en.wikipedia.org/wiki/Multics) at MIT, GE and Bell Labs.

* **In the beginning** - early 1970s, Nixon drags on, Watergate, Bell Labs,
[Thompson](https://en.wikipedia.org/wiki/Ken_Thompson) &
[Ritchie](https://en.wikipedia.org/wiki/Dennis_Ritchie),
[UNIX](https://en.wikipedia.org/wiki/History_of_Unix),
blah blah blah...

* **More Trouble From Berkeley** - late 1970s, Carter, disco, Iran
hostages, UC Berkely releases the [Berkeley Software
Distribution](https://en.wikipedia.org/wiki/Berkeley_Software_Distribution)
(BSD), a port based on the Bell Labs UNIX. Let the forking begin!

* **Goes commercial** - 1980s, Reagan, Iran Contra, *E.T.*, AT&T releases
[System V](https://en.wikipedia.org/wiki/UNIX_System_V) as first commercial
UNIX. From the same background as Bell Labs UNIX, but evolved with subtle
and not so subtle differences in approaches to command syntax, networking
and much more. It is this release and AT&T's copyrights that are the basis
of all the SCO-vs-Linux lawsuits 2-3 decades later.

* **Explosion of *IX** -late 1980s/early 1990s, Bush I, Berlin Wall falls,
Gulf War I, proliferation of proprietary (and different) "UNIX" platforms:

    * **HP HP-UX**
    * **Sun SunOS** - BSD flavor.
    * **Sun Solaris** - System V flavor. Now Oracle Solaris.
    * **IBM AIX**
    * **SGI IRIX**
    * **...and many, many more!** - although mostly all that's left now is
    HP-UX, AIX and Solaris.


* **Linux** - 1991+, Clinton I, grunge, *Titanic*, [Linus
Torvalds](https://en.wikipedia.org/wiki/Linus_Torvalds) releases
a project called [Linux](https://en.wikipedia.org/wiki/Linux) based on
[MINIX](https://en.wikipedia.org/wiki/MINIX)
(and hence why Linus says Linux is pronounced like "MINIX" and not like
"Linus"). **Note:** By 1996 I was running an early Linux version
([Slackware](https://en.wikipedia.org/wiki/Slackware)
[distro](https://en.wikipedia.org/wiki/Linux_distribution)) on a laptop
with 8MB of memory!

* **Proliferation of the BSDs** - mid-to-late 1990s, still Clinton I,
Monicagate, Kosovo, various ports of BSD including
[NetBSD](https://en.wikipedia.org/wiki/NetBSD),
[FreeBSD](https://en.wikipedia.org/wiki/FreeBSD) and
[OpenBSD](https://en.wikipedia.org/wiki/OpenBSD), all happen in the same
time frame as Linux. Like Linux distros, each has its own focus and
prejudices, some of which are distinctly "anti-Linux." The "big three" are
all still in heavy use today, especially among ISPs. The perception is
still out there among a generation of sysadmins that Linux is for the
desktop and BSDs for servers, but that reality shifted a long time ago.

* **Ports of call** - 2000+, Bush II & Obama, Afghanistan & Gulf War 2,
lots of cross-porting of everything open source. However, [licenses
matter](https://en.wikipedia.org/wiki/Open-source_license),
and [there sure are a lot of
them](https://en.wikipedia.org/wiki/Comparison_of_free_and_open-source_software_licenses). While it has settled down some with the
dismissal of the SCO lawsuit, intellectual property remains a problem area
in open source, even as its use has exploded.

**Q:** So, what's Linux? Or BSD? Or even UNIX?

**A:** Depends on who you're asking and in what context!

To further muddy the waters, there have been multiple attempts to
"standardize" whatever it is this thing is called:

* [**POSIX**](https://en.wikipedia.org/wiki/POSIX) - a de jure set of
standards created in the 1980s and 1990s to try to bring order to the chaos
that was commercial UNIX-flavored operating systems of the time. It worked.
Sorta. Especially once the US government started wanting systems to be
"POSIX-compliant." **Note:** No system runs POSIX, they all are "similar
but different." Even Windows can claim to be POSIX in some respects (and
has an installable POSIX subsystem), but that doesn't mean POSIX-compliant
code will run there unchanged.

* [**GNU Project**](https://en.wikipedia.org/wiki/GNU_Project) - [Richard
Stallman](https://en.wikipedia.org/wiki/Richard_Stallman) founded the
[Free Software
Foundation](https://en.wikipedia.org/wiki/Free_Software_Foundation) (FSF)
and GNU project in the mid-1980s, ***long*** before Linux (GNU = "GNU's Not
Unix"). The GNU project delivers [a suite of programs and
tools](https://www.gnu.org/software/software.html), many of which are
used in both Linux and BSD variants as de facto standards.

* **Various Linux Efforts** - there have also been various movements over
the years, some more successful than others, to "standardize" Linux or some
part of it, such as the file system layout, the `init` system,
documentation, and now even what is part of the most basic "core OS" for
things like better containerization.

For the rest of this we will use "Linux" or "*IX" to stand in for a
"generic" UNIX-flavored OS unless a difference is specifically called out.

## Why Does This Matter?

Because there are various "flavors" of commands and tools, based on whether
you're dealing with a System V (Linux) or BSD (Free/Net/Open) descendant.
Some of the OS versions are strong in security, or networking, or as a
desktop. Certain things are "built-in" to the operating system but most are
installed as packages, and depending on the source of the package it may
or may not work correctly on another "*IX" system without effort.

It is similar to the history and relationship between `COMMAND.EXE` in DOS
and `CMD.EXE` in Windows 10, where this would work in both:

```
COPY A.TXT B.TXT
```

But only the later, network-and-NTFS-aware `CMD.EXE` could handle:

```
COPY "My 2015 Tax Returns.pdf" \\MyServer\Finances\.
```

In *IX-land over time these differences seem to be getting better, but
there are still "gotchas," often involving the differences in open source
licenses in the underlying code. There are fundamental differences and
assumptions between the "GNU" and "GPL" licenses on the one side and "MIT"
and "BSD" licenses on the other. I am not a lawyer, but I would summarize:

* **FSF/GNU/GPL** - mostly concerned with keeping open source "open," that
is sharable and modifiable by all.

* **BSD & MIT** - more focused on letting anyone do anything to the code as
long as the original author is acknowledged and liability released.

The best thing is to be vaguely aware of this history and licenses and if
something isn't available on a certain platform or if a command isn't
taking a specific parameter to search for variants.

For example, note the differences in command line parameters and output
between showing all processes with the
[`ps`](http://linux.die.net/man/1/ps) (*process*) command on a Linux
system, in this case Linux Mint:

```bash
$ ps -AH
  PID TTY          TIME CMD
    2 ?        00:00:00 kthreadd
    3 ?        00:00:00   ksoftirqd/0
    5 ?        00:00:00   kworker/0:0H
    7 ?        00:00:19   rcu_sched
    8 ?        00:00:04   rcuos/0
    9 ?        00:00:09   rcuos/1
   10 ?        00:00:07   rcuos/2
...and so on...
```
...versus on a FreeBSD system at my ISP, where `csh` is the default shell:

```bash
%ps -ax
  PID  TT  STAT      TIME COMMAND
73591  ??  S      0:00.03 sshd: myuser@ttyp1 (sshd)
79503  ??  S      0:00.07 dovecot/imap
80065  ??  S      0:00.05 dovecot/imap
73593  p1  Ss     0:00.02 -csh (csh)
90737  p1  RN+    0:00.00 ps -ax
```

To make things even more confusing, the Linux version of `ps` has been
written to understand the BSD-style syntax and flags, too!

## Panic at the Distro

Remember that "Linux," FreeBSD, OpenBSD and NetBSD are all really just OS
kernels, boot loaders, drivers and enough functionality to get a computer
up and running. Most functionality comes via other "packages." From almost
the beginning there have been alternative approaches to both what packages
should (and should not) be included, as well as how best to manage the
installing, updating and removal of those packages.

In the BSD world each major port has its own approach. In the Linux world
the job of deciding all this and putting it all together falls to
distributions or "distros." These have evolved over time into a series of
["families"](https://en.wikipedia.org/wiki/Linux_distribution#Popular_distributions)
based in large part around the
[package management tool](https://en.wikipedia.org/wiki/Package_manager)
predominantly used:

* **`apt-get`, `dpkg` and `.deb` files** -
[Debian](https://en.wikipedia.org/wiki/Debian) flavors, such as
[Ubuntu](https://en.wikipedia.org/wiki/Ubuntu_%28operating_system%29) and
[Mint](https://en.wikipedia.org/wiki/Linux_Mint) (Mint is currently my
desktop Linux of choice, Debian my preferred server OS, but both solely
based on familiarity).

* **`pacman`** - [Arch](https://en.wikipedia.org/wiki/Arch_Linux) flavors.

* **`rpm` and `yum`** - Red Hat flavors, such as
[Fedora](https://en.wikipedia.org/wiki/Fedora_%28operating_system%29), [Red
Hat Enterprise](https://en.wikipedia.org/wiki/Red_Hat_Enterprise_Linux) and
[CentOS](https://en.wikipedia.org/wiki/CentOS).

* **Source code** - [Gentoo](https://en.wikipedia.org/wiki/Gentoo_Linux)
tends to be a "compile from scratch" environment, much like
[FreeBSD](https://en.wikipedia.org/wiki/FreeBSD_Ports).

* **"Tar balls"** - source code or binaries delivered via archived and
zipped directories. Common on
[Slackware](https://en.wikipedia.org/wiki/Slackware), some others.

## Get Embed With Me

A lot of firmware in embedded devices is based on some sort of *IX flavor.
Networking gear at both the consumer and enterprise level, storage devices
and so on all tend to run something that "looks like" UNIX at some level.
Of course, as to what's actually available, who knows? If you can get
a shell (command prompt) the best thing to do is see what works.

## Cygwin

[Cygwin](http://cygwin.com/) is an interesting beast. It is a DLL for
Windows that implements most of the POSIX and related UNIX-like "system API
calls" for programming, and then is also a series of ported open source
packages, including shells, utilities and even desktop environments, all
***recompiled*** to run on Windows as long as the Cygwin DLL is accessible.
Like a Linux distro it has an installer that is a "package manager," and if
a package isn't available, you can usually recompile the source code using
Cygwin.

You cannot run Linux or BSD binaries on Cygwin without recompiling
them first.  **However**, you can often run ***scripts*** from a Linux
environment on Cygwin with little or no tweaking. Which means you can
then take advantage of a lot of excellent open source tools simply by
installing their packages in Cygwin and running scripts against them.

Ultimately, though, Cygwin is of limited use, basically for getting to
some open source tools on Windows without having to set up a Linux
box. You can do a lot of amazing things with Cygwin with enough effort
(including running X and a desktop environment like GNOME!), but at some
point why not expend that effort in standing up a "real" Linux (virtual)
machine anyway?