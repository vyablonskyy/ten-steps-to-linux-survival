# Step 10. And So On

***`/etc`, starting and stopping services, `apt-get`/`rpm`/`yum`, and
more.***

> *"Et cetera, et cetera, et cetera!"* - The King (*The King and I*)

This step is a grab bag of stuff that didn't seem to directly belong
anywhere before, but I still think needs to be known, or at least brushed
up against.

## One-Stop Shopping

In UNIX-like systems, most (not all) system configuration is stored in
directories and text files under `/etc`.

**Note:** In Linux almost universally `/etc` is pronounced "slash-et-see,"
***not*** "forward slash et cetera."

```bash
# ls -l /etc
total 844
drwxr-xr-x 3 root root    4096 Feb 25  2015 acpi
-rw-r--r-- 1 root root    2981 Apr 23  2014 adduser.conf
-rw-r--r-- 1 root root      45 Jul  9 08:46 adjtime
-rw-r--r-- 2 root root     621 May 22  2014 aliases
-rw-r--r-- 1 root root   12288 May 22  2014 aliases.db
drwxr-xr-x 2 root root   20480 Feb 25  2015 alternatives
-rw-r--r-- 1 root root    4185 Dec 28  2011 analog.cfg
drwxr-xr-x 7 root root    4096 Feb 25  2015 apache2
drwxr-xr-x 6 root root    4096 Feb 25  2015 apt
-rw-r----- 1 root daemon   144 Jun  9  2012 at.deny
-rw-r--r-- 1 root root    1895 Dec 29  2012 bash.bashrc
...and so on...
```

Depending on what you are trying to configure, you may be in one or many
files in `/etc`. This is a ***very short*** list of files and directories
you may need to examine there:

* **`fstab`** - a listing of the file systems currently mounted and their
types.

* **`group`** - the security groups on the system.

* **`hosts`** - network aliases (overrides DNS, takes effect immediately).

* **`init.d`** - startup and shutdown scripts for "services."

* **`mtab`** - list of current "mounts."

* **`passwd`** - "shadow" file containing all the user accounts on the
system.

* **`resolv.conf`** - DNS settings.

* **`samba`** - file sharing settings for CIFS-style shares.

There are lots of other interesting files under `/etc`, but I keep
returning to the above again and again. On most of them you can run the
`man` command against section 5 to see their format and documentation,
e.g., `man 5 hosts`.

## Service Station

We are going to ignore system initialization and "stages," and assume most
of the time you are running on a well-functioning system. Even so sometimes
you want to restart a specific system service without rebooting the whole
system, often to force re-reading changed configuration files. If the
service has a script in `/etc/init.d`:

```bash
# ls /etc/init.d
acpid                   console-setup  kbd                    mountkernfs.sh         nginx          README        sendsigs       udev-mtab
apache2                 cron           keyboard-setup         mountnfs-bootclean.sh  openbsd-inetd  reboot        single         umountfs
atd                     dbus           killprocs              mountnfs.sh            postfix        redis-server  skeleton       umountnfs.sh
bootlogs                exim4          kmod                   mpt-statusd            postgresql     rmnologin     smartd         umountroot
bootmisc.sh             gitlab         motd                   mtab.sh                procps         rpcbind       smartmontools  urandom
checkfs.sh              halt           mountall-bootclean.sh  networking             rc             rsync         ssh            winbind
checkroot-bootclean.sh  hostname.sh    mountall.sh            nfs-common             rc.local       rsyslog       sudo
checkroot.sh            hwclock.sh     mountdevsubfs.sh       nfs-kernel-server      rcS            samba         udev
```

...then chances are it will respond to a fairly standard set of commands,
such as the following samples with `samba`:

```bash
# /etc/init.d/samba stop
[ ok ] Stopping Samba daemons: nmbd smbd.

# /etc/init.d/samba start
[ ok ] Starting Samba daemons: nmbd smbd.

# /etc/init.d/samba restart
[ ok ] Stopping Samba daemons: nmbd smbd.
[ ok ] Starting Samba daemons: nmbd smbd.
```

**Note:** The above examples were run as `root`, otherwise they would
probably have required execution using `sudo`.

## Package Management

Almost all Linux distros have the concept of "packages" which are used to
install, update and uninstall software. There are different package
managers, including `dpkg` and `apt-get` on Debian-based distros, `rpm` on
Fedora descendants, etc. For the rest of this section we will use Debian
tools, but in general the concepts and problems are similar for the other
toolsets.

One of the nicest things about Linux-style package managers (as opposed to
traditional Windows installers) is that they can satisfy all a packages
"dependencies" (other packages that are required for a package to run)
and automatically detect and install those, too. See
[Chocolately](https://chocolatey.org/) for an attempt to build a similar
ecosystem in Windows.

One thing Linux distros do is define the "repositories" (servers and file
structures) that serve the various packages. In addition, there are
usually multiple versions of packages, typically matching different
releases of the distro. We won't go into setting up a system to point to
these here.

In Debian flavors, [`apt-get`](http://linux.die.net/man/8/apt-get) is
usually the tool of choice for package management.

There are three common `apt-get` commands that get used over and over. The
first downloads and *updates* the local metadata cache for the
repositories:

```bash
$ sudo apt-get update
[sudo] password for myuser: 
Ign http://packages.linuxmint.com rafaela InRelease
Hit http://packages.linuxmint.com rafaela Release.gpg                          
Ign http://extra.linuxmint.com rafaela InRelease                               
Ign http://archive.ubuntu.com trusty InRelease                                 
Hit http://security.ubuntu.com trusty-security InRelease                       
Hit http://packages.linuxmint.com rafaela Release                              
Hit http://extra.linuxmint.com rafaela Release.gpg                             
Hit http://archive.ubuntu.com trusty-updates InRelease                         
Hit http://extra.linuxmint.com rafaela Release                
...and so on...
```

**Note:** `apt-get` is an administrative command and usually requires
`sudo`.

The second common command *upgrades* all the packages in the system to
the latest release in the repository (which may not be the latest and
greatest release of the package):

```bash
$ sudo apt-get dist-upgrade
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

In this case there was nothing to upgrade. And the final common command
is obviously to install a package:

```bash
$ sudo apt-get install curl
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  curl
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 123 kB of archives.
After this operation, 314 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu/ trusty-updates/main curl amd64 7.35.0-1ubuntu2.5 [123 kB]
Fetched 123 kB in 0s (312 kB/s)
Selecting previously unselected package curl.
(Reading database ... 182823 files and directories currently installed.)
Preparing to unpack .../curl_7.35.0-1ubuntu2.5_amd64.deb ...
Unpacking curl (7.35.0-1ubuntu2.5) ...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Setting up curl (7.35.0-1ubuntu2.5) ...
```

You can also `remove` packages.

This all looks very convenient, and it is. The problems arise because some
distros are better at tracking current versions of packages in their
repositories than others. In fact, some distros purposefully stay behind
cutting edge for system stability purposes.

## Other Sources

Besides the distribution's repositories, you can install packages and
other software from a variety of places. It may be an "official" site for
the package, GitHub, or whatever. The package may be in a binary
installable format (`.deb` files for Debian systems), in source format
requiring it to be built, in a zipped "tarball," and more.

If you want the latest and greatest version of a package you often have to
go to its "official" site or GitHub repository. There, you may find a
`.deb` file, in which case you could install it with `dpkg`:

```bash
sudo dpkg -i somesoftware.deb
```

There is, however, a problem. You now have to remember that you installed
that package by hand and keep it up to date by hand (or not). `apt-get
upgrade` isn't going to help you here. This is true no matter what way
you get the alternative package - `.deb` file, tarball, source code, or
whatever.

The final problem with package managers is that they're such a good idea
that everybody has them now. Not just the operating systems like Linux,
but languages like Python have [`pip`](https://pypi.python.org/pypi/pip/)
and execution environments like node have [`npm`](https://www.npmjs.com/).
So now you end up with having to keep track of what you have installed
on a system across two or three or more package managers at different
levels of abstraction. It can be a mess!

Add into this that many of these language and environment package managers
allow setting up "global" (system-wide) or "local" (current directory)
versions of a package to allow different versions of the same package to
exist on the same system, where different applications may be relying on
the different versions to work.

## Which `which` is Which?

Now that we've seen that we can have multiple versions of the same command
or executable on the system, an interesting question arises. *Which* `foo`
command am I going to call if I just type `foo` at the command prompt? In
other words, after taking the `$PATH` variable into consideration and
searching for the program through that from left to right, which version
in which directory is going to be called?

Luckily we have the [`which`](http://linux.die.net/man/1/which) command
for just that!

```bash
$ which curl
/usr/bin/curl
```

How can you tell if you have multiple versions of something installed?
One way is with the [`locate`](http://linux.die.net/man/1/locate) command:

```bash
locate md5
/boot/grub/i386-pc/gcry_md5.mod
/lib/modules/3.16.0-38-generic/kernel/drivers/usb/gadget/amd5536udc.ko
/usr/bin/md5pass
/usr/bin/md5sum
/usr/bin/md5sum.textutils
/usr/include/libavutil/md5.h
/usr/include/openssl/md5.h
/usr/lib/casper/casper-md5check
/usr/lib/grub/i386-pc/gcry_md5.mod
/usr/lib/i386-linux-gnu/sasl2/libcrammd5.so
...and so on...
```

The `locate` command, if installed, is basically a database of all of the
file names on the system (collected periodically - not real time). You are
simply searching the database for a pattern.

One final note on which thing gets executed. Unlike in Windows, UNIX
environments do not consider the local directory (the current directory
you are sitting at the command prompt, i.e., what
[`pwd`](http://linux.die.net/man/1/pwd) shows) as part of the path unless
`.` is explicitly listed in `$PATH`. This is for security purposes. So it
can be a bit unnerving to try and execute `foo` in the current directory
and get:

```bash
$ ls -l foo
-rwxrwx--- 1 myuser mygroup 16 Oct 23 19:03 foo

$ foo
No command 'foo' found, did you mean:
 Command 'fgo' from package 'fgo' (universe)
 Command 'fop' from package 'fop' (main)
 Command 'fog' from package 'ruby-fog' (universe)
 Command 'fox' from package 'objcryst-fox' (universe)
 Command 'fio' from package 'fio' (universe)
 Command 'zoo' from package 'zoo' (universe)
 Command 'xoo' from package 'xoo' (universe)
 Command 'goo' from package 'goo' (universe)
foo: command not found
```

Instead, to invoke `foo`, you can either fully qualify the path as shown
by `pwd`:

```bash
$ /home/myuser/foo
```

Or you can prepend the `./` relative path to it, to indicate "the `foo` in
the current directory (`.`)":

```bash
$ ./foo
```

## Over and Over and Over

The function of scheduled tasks in Windows is performed by
[`cron`](http://linux.die.net/man/8/cron). It reads in the various
[`crontab(5)`](http://linux.die.net/man/5/crontab) files on the system
and executes the commands in them at the specified times. You use the
[`crontab(1)`](http://linux.die.net/man/1/crontab) command to view and
edit the `crontab` files for you and other users (if you have admin
privileges).

The sample given in the comments of the `crontab` when initially opened
using `crontab -e` give a fine example of the syntax of the `crontab`
file:

```
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
```

If you have `sudo` privileges you can edit the `crontab` file for another
user with:

```bash
$ sudo crontab -e -u otheruser
```

This can be useful to do things like run backup jobs as the user that is
running the web server, say, so it has access rights to all the necessary
files to back up the web server installation by definition.

The only other thing I have to add about `cron` is when it runs the
commands from each `crontab`, they are typically not invoked with that
particular user's environment settings, so it is best to fully specify
the paths to files both in the `crontab` file itself and in any scripts
or parameters to scripts it calls. Depending on the system and whether
`$PATH` is set at all when a "`cron` job" runs, you may have to specify
the full paths to binaries in installed packages or even what you would
consider "system" libraries! The `which` command comes in handy here.

## Start Me Up

If you need to reboot the system the quickest way is with the
[`reboot`](http://linux.die.net/man/8/reboot) command:

```bash
$ sudo reboot
```

You can also use the [`shutdown`](http://linux.die.net/man/8/shutdown)
command with the `-r` option, but why? The handy use for `shutdown` is to
tell a system to halt and power off after shutting down:

```bash
$ sudo shutdown -h now
```

## Turn on Your Signals

One of the basic concepts in UNIX program is that of
["signals"](https://en.wikipedia.org/wiki/Unix_signal). You are probably
already familiar with one way to send signals to a program, which is via
`Ctrl-C` at the command prompt, which sends the `SIGINT` ("interrupt")
signal to the program. Typically this will cause a program to terminate.

However, most signals can be "caught" by a program and coded around. There
is one "uninterruptable" signal, however, which is `SIGKILL`. We can send
`SIGKILL` to a process and cause it to terminate immediately with:

```bash
kill -s 9 14302
```

The `-s 9` is for signal #9, which is the `SIGKILL` signal (it
is the tenth signal in the signal list, which is 0-relative, hence #9).

You can also use the following "shorthand" for `SIGKILL`:

```bash
kill -9 14302
```

Or if you want to get all verbose:

```bash
kill -s SIGKILL 14302
```

**Note:** `SIGKILL` should be used as a last resort, because a program is
not allowed to catch it or be notified of it and hence can perform no
closing logic or cleanup and may lead to data corruption. It is for getting
rid of "hung" processes when nothing else will work. Always try to stop a
program with a more "normal" method, which can include sending `SIGINT` to
it first.

## Exit, Smiling

Sometimes a command runs and there isn't a good way to tell if it worked
or not. UNIX programs are supposed to set an "exit status"
when they end that by convention is `0` if the program exited successfully
and a non-zero, typically positive number if there was an error. The exit
status for the last executed command or program can be shown at the
command line using the `$?` environment variable. Consider if the file
`foo` exists and `bar` does not:

```bash
$ ls foo
foo

$ echo $?
0

$ ls bar
ls: cannot access bar: No such file or directory

$ echo $?
2
```

**Note:** In many cases the exit codes come from the ANSI Standard C
library's [`errno.h` file](http://mazack.org/unix/errno.php). All of this
is much handier when handling errors in scripts, but we're not going to go
into script logic here.

However, sometimes even at the command line we want to be able to
conditionally control a sequence of commands, and continue (or not
continue) based on the success (or failure) of a previous command. In
`bash` we have `&&` and `||` to the rescue!

* **`a && b`** - execute `a` ***and*** `b`, i.e., execute `b` only if `a`
is successful.

* **`a || b`** - execute `a` ***or*** `b`, that is execute `b` whether
***or*** not `a` is successful.

Our example of file `foo` (which exists) and file `bar` (which does not)
and the effect on the exit code of `ls` can be illustrative here, too:

```bash
$ ls foo && ls bar
foo
ls: cannot access bar: No such file or directory

$ echo $?
2
```

Both `ls` commands execute because the first successfully found `foo`,
but the second emits its error and sets the exit code to `2` (failure).

```bash
$ ls foo || ls bar
foo

$ echo $?
0
```

Note in this case the second `ls` didn't execute because the logical "or"
condition was already satisfied by the successful execution of the first
`ls`. The exit code is obviously `0` (success).

```bash
$ ls bar && ls foo
ls: cannot access bar: No such file or directory

$ echo $?
2
```

Obviously if the first command fails, the "and" condition as a whole
fails and the expression exits with a code of `2`.

```bash
$ ls bar || ls foo
ls: cannot access bar: No such file or directory
foo

$ echo $?
0
```

And finally, while the first command failed the second still can execute
because of the "or", and the whole expression returns `0`.

**Note:** There is actually a [`true`](http://linux.die.net/man/1/true)
command whose purpose is to, "do nothing, successfully." All it does is
return a `0` (success) exit code. This can be useful in scripting and
also sometimes when building "and" and "or" clauses like above.

And yes, of course, that means there is also a
[`false`](http://linux.die.net/man/1/false) command to "do nothing,
unsuccessfully!"

```bash
$ true

$ echo $?
0

$ false

$ echo $?
1
```

## The End

Now you know what I know. Or at least what I keep loaded in my head vs.
what I simply search for when I need to know it, and you know how to do
that searching, too.

Good luck, citizen!