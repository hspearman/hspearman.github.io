- [Ethos](#ethos)
- [`/root`](#root)
  - [`/bin`](#bin)
  - [`/boot`](#boot)
  - [`/dev`](#dev)
  - [`/etc`](#etc)
  - [`/home`](#home)
  - [`/lib`](#lib)
  - [`/media`](#media)
  - [`/mnt`](#mnt)
  - [`/opt`](#opt)
  - [`/proc`](#proc)
  - [`/root`](#root-1)
  - [`/run`](#run)
  - [`/sbin`](#sbin)
  - [`/srv`](#srv)
  - [`/sys`](#sys)
  - [`/tmp`](#tmp)
  - [`/usr`](#usr)
    - [`/bin`](#bin-1)
    - [`/lib`](#lib-1)
    - [`/local`](#local)
    - [`/sbin`](#sbin-1)
    - [`/share`](#share)
  - [`/var`](#var)
    - [`/log`](#log)
    - [`/tmp`](#tmp-1)
- [Addendum](#addendum)
  - [Filesystem Hierarchy Standard](#filesystem-hierarchy-standard)
  - [Archaism](#archaism)


# Ethos

It's been a continual goal of mine to gain a more cohesive understanding of Linux. As a small step towards that goal, I set out to understand the philosophy behind its hierarchy of files and directories.

If you're not a Linux wizard, it can be daunting to understand the intention behind its tersely-named directories: three-to-four letter names like `opt` and `etc` don't always make it easy to grok their purpose.

I'm not a Linux wizard myself. Though, I strive to understand its madness on my journey to become a more holistic software engineer. Beyond shear curiosity, Linux exhibits many clever design decisions that may influence the way you approach other problems (see my previous post about [applying unix philosophy to your note-taking setup](/2018/12/30/how-to-take-notes/)).

I wrote this layman's guide to not only solidify my own understanding, but explain the basics of each directory to the other Linux laymen of the world as well.

Let's dive in.

# `/root`

In a linux terminal, `/` denotes the root directory: it's the highest point in the hierarchy that houses all other directories below it.

Underneath root, you'll find a collection of directories each with their own purpose:

```
.
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── run
├── sbin
├── srv
├── sys
├── tmp
├── usr
│   ├── bin
│   ├── lib
│   ├── local
│   ├── sbin
│   ├── share
└── var
    ├── log
    ├── tmp
```

Let's explore what each of these directories are for.

_NOTE: This guide is not comprehensive of every directory in the [Filesystem Hierarchy Standard](#filesystem-hierarchy-standard), but I'll attempt to cover most of them to the best of my ability._

## `/bin`

`/bin` contains many of the **common built-in executable programs** such as `mv`, `ln`, etc. These executables are essential building blocks that the system requires to minimally operate.

If you `echo $PATH`, you'll notice that `$PATH` includes `/bin` in its string of directories. When you type a command such as `ls`, `$PATH` instructs your system where to look for an executable program that matches the given command. As such, `/bin` is likely where your system finds and runs a lot of common commands such as `ls`, `mv`, and otherwise.

## `/boot`

`/boot` contains files necessary to the system boot process.

_NOTE: The Linux boot process is outside the scope of this article. For now, just be aware that this directory is imperative to it._

## `/dev`

`/dev` is a **virtual file system** that contains device files. To understand what a virtual file system is, see [`/proc`](#proc).

Device files provide an interface that allows users to interact with devices. Typically, they represent _physical_ devices like disk drives, modems, RAM, etc.

Though, they are also _pseudo_ device files that implement the interface but do not represent actual hardware. For example, `/dev/urandom` is a "device" file that attempts to gather random environmental noise from device drivers, populates an entropy pool, then generates random output from it:

```
~$ cat /dev/urandom
uKϯՙ, ;-3MǦ)bL/c0qbUG Xh:8IES
```

## `/etc`

`/etc` contains system configuration files. Generally, these files are **not executable** but simply **configure how your system operates**.

For example, `/etc/hosts` contains IP addresses that map to host names. When the system tries to fetch a URL such as `foobar.com`, it contacts a DNS server to resolve the host name `foobar.com` to an actual IP address. But before it does that, the system checks if the host name exists locally in `/etc/hosts`.

Let's say I have a local web server running on port 5555 that I want to address by `foobar.com`. If I add `127.0.0.1 foobar.com` to `/etc/host` and `curl foobar.com:5555`, the system resolves that host name to my local web server due to my entry in the configuration file.

This is just one example, but `/etc` contains many other files that dictate system-wide configurations.

## `/home`

`/home` contains a directory for each user on the system. A user's home directory contains personal files as well as user-specific configuration files for applications on your system.

These configuration files are often referred to as "dot files" since they start with '.'. For example, `~/.bashrc` and `~/.bash_profile` configure how your console behaves.

_NOTE: `~/` is simply a shortcut for `/home/yourusername/`._

## `/lib`

`/lib` contains shared library images and kernel modules that help the system boot.

**Shared libraries** contain pieces of C code used by programs to perform common tasks.

You can easily identify shared libraries by their `.so` or `.a` suffix: `.so` signifies a dynamic library while `.a` signifies a static library.

Executables in `/bin` and `/sbin` often include these libraries when they link or compile to leverage the code they contain. For example, `libc.so.6` is a dynamic library that contains many standard C functions such as `malloc`, `read`, `open`, etc. If an executable wants to open a file via `open`, it links this shared library.

_NOTE: The numbers that follow `.so` denote the shared library's version._

_NOTE: Dynamic vs. static libraries is outside the scope of this article. For now, just be aware that they exist._

**Kernel modules** contains pieces of compiled C code used by the kernel to extend it's functionality.

You can easily identify kernel modules by their `.ko` suffix.

To put it simply, they are special shared libraries used solely by the kernel.

## `/media`

`/media` contains external file systems from removable devices that the system may automatically mount for you (ex. USB flash drives, CD-ROMs, etc.). This is opposed to `/mnt` where you manually mount external file systems yourself.

**Mounting** is the process that makes external file system available to your system.  

## `/mnt`

`/mnt` contains external file systems that you temporarily and manually mount yourself. This is opposed to `/media` where the system may automatically mount removable media such as USB flash drives, CD-ROMs, etc.

For example, you could mount a remote file system over SSH in this directory for easy access.

## `/opt`

`/opt` contains third-party software that do not exist in the default installation. These programs typically do not abide by Unix standards. As such, they are usually self-contained and live in a single directory named after itself.

## `/proc`

`/proc` is a **virtual file system** that represents information about running processes and other system data. The files it contains aren't actually _real_: they are an abstraction used to represent information about your system.

To reiterate, these files are not actual data stored on disk: `ls -l /proc` shows that the size of almost every file is 0. Let's explore what this means.

On Linux, everything is a file. A file is simply an interface to a stream of bytes and does not necessarily represent static data that lives somewhere on disk.

Why does Linux abstract the concept of files to such a wide definition? Think about it from the perspective of a programmer: it's simpler to write code and tools for a single interface rather than `n` arbitrarily different interfaces. At the end of the day, you just want to interact with data and probably don't care where it actually lives.

`/proc` leverages this interface to make data about your system accessible. Specifically, it contains a directory named after every running process' ID or **PID**. For example, let's locate the corresponding folder for `cron` by it's PID:

```
~$ ps aux | grep cron
root       798  0.0  0.1  23652   636 ?        Ss    2019   0:07 cron
...
~$ ls /proc | grep 798
798
```

This directory has files that contain information about PID 798. For example, let's print the contents of `/proc/798/status` to see data about the process' status.

```
~$ cat /proc/798/status
Name:   cron
State:  S (sleeping)
Tgid:   798
Ngid:   0
Pid:    798
...
```

This example just scratches the surface. For now, just be aware that `/proc` is a special directory that let's you access data about your system.

## `/root`

`/root` is the root user's home directory. It serves the exact same purpose as `/home/yourusername` but it exists as a top-level directory instead of nested under `/home`.

## `/run`

`/run` contains temporarily files created by running programs. It's a fairly new directory established in 2011 that's similar to `/tmp` but slightly different. From my understanding, limits privileges a bit more than `/tmp` (which can be a bit of a wild west).

_NOTE: Since `/run` is meant to be ephemeral, your system cleans out it's contents on reboot._

## `/sbin`

`/sbin` contains many **built-in executable programs** such as `reboot`, `ln`, etc. It's very similar to `/bin`, but unlike `/bin`, `$PATH` does not typically include it because the executables concern system administration (and thus should not be universally discoverable).

For example, `reboot` is an executable that restarts the system. Since a system reboot is a very administrative action, it lives in `/sbin` away from less disruptive programs like `ls`.

_NOTE: From my understanding, `/sbin` was not split off from `/bin` for security reasons. In fact, all users likely have read and execute permissions on its executables. It's merely a way to separate common everyday utilities from system administrative ones._

## `/srv`

`/srv` contains data for specific services that require a self-contained directory. You'll often find the subdirectories named by protocol such as `ftp`, `rsync`, `www`, etc.

For example, FTP may create a `/srv/ftp` directory here. FTP stands for **File Transfer Protocol** which allows other computers to upload or download files from a server. By default, it may host `/srv/ftp` as a directory to upload and download files.

## `/sys`

`/sys` is a **virtual file system** that represents information about devices, drivers, the kernel, and more. To understand what a virtual file system is, see [`/proc`](#proc).

For example, you can inspect the size of the CPU's L1 cache:
```
~$ cat /sys/devices/system/cpu/cpu0/cache/index0/size
32K
```

## `/tmp`

`/tmp` contains temporarily files created by running programs. For example, some programs may create lock files here to ensure that only one thread of execution performs a certain task at a time. Other programs may create files here to store temporary data that only needs to exist for a short period of time.

_NOTE: Since `/tmp` is meant to be ephemeral, it's likely that your system cleans out it's contents on reboot (but this behavior is not guaranteed)._

## `/usr`

`/usr` likely holds the lion's share of data on your system: it contains many user-space programs and data.

You can think of user space as a "sandbox": programs run with lower privileges than those that run in kernel space. This is a bit of an oversimplification (and frankly, a topic that deserves it's own blog post)-- but for now, just be aware of the distinction.

### `/bin`

`/usr/bin` is similar to [`/bin`](#bin), but contains executables that are not required on boot.

### `/lib`

`/usr/lib` is similar to [`/lib`](#lib), but contains libraries that are not required on boot.

### `/local`

`/usr/local` contains third-party software that does not exist in the default installation. If you think this sounds very similar to `/opt`, you'd be correct! 

From my understanding, the topic of whether to use `/opt` vs. `/usr/local` is a classic holy war amongst Linux wizards. I won't get into the weeds in this post, but [this article](https://www.linuxjournal.com/magazine/pointcounterpoint-opt-vs-usrlocal) documents a fun debate on the topic.

### `/sbin`

`/usr/sbin` is similar to [`/sbin`](#sbin), but contains system administrative executables that are not required on boot.

### `/share`

`/usr/share` contains files (ex. fonts, manual pages, etc.) that do not depend on your system's architecture (ex. x86_64, ARM, etc.).

Different companies manufacture CPUs that understand different sets of instructions (or "architectures"). As such, programmers often compile their program to support multiple architectures, and place non-compilable files in `/usr/share` since they are architecture-independent.

From my understanding, this folder is a bit of a relic. In the past, system administrators would share this directory across networks of machines to save space. Though, disk space is cheap these days so people rarely do this anymore.

## `/var`

`/var` contains variable data files (as opposed to `/usr` which contains static data files). Programs often use this directory to record runtime information.

### `/log`

`/var/log` contains log files that document the chronology of events that occur while a program runs.

For example, if a user fails to authenticate and you're not sure why, you could `tail -f /var/log/auth.log` for some hints.

### `/tmp`

`/var/tmp` is similar to [`/tmp`](#tmp), but preserves files between reboots.

# Addendum

## Filesystem Hierarchy Standard

The **Filesystem Hierarchy Standard**, or FHS for short, is a set of guiding principles written by the Linux Foundation to standardize the location and purpose of directories and their associated files.

I sourced much of my knowledge from the FHS (supplemented by information from other sources as well). If you'd like to know more or learn about a directory I did not cover here, seek out this standard for more information.

## Archaism

When you learn about Linux, it's important to understand that it continually evolves since its release almost 30 years ago. As a result, **archaic design decisions may persist alongside newer iterations of the same idea**.

For example, `/proc` started as a virtual filesystem to contain information about running processes. Over time, it exposed system-level data as well (ex. `cpuinfo`, etc.). Since this cluttered `/proc`'s original focus on individual processes, `/sys` was created to house system-level data exclusively. Though, a lot of the same system-level data still exists in `/proc` to maintain compatibility with programs that expect it there.

Linux was not immediately created in its final form, and often lives in a transitional state between old and new ideas. So if the distinction between certain directories feels unclear, it may be a result of this continual evolution.

Beyond the context of Linux, **archaism is everywhere**.

JavaScript was popularized as the language that all browsers understand despite its notorious reputation. To address the flaws of JavaScript, TypeScript was invented: a language that sits "on-top" of JavaScript. If you did not know the history of JavaScript as the arbitrarily standard language of all browsers, the fact that TypeScript compiles to JavaScript might seem incredibly bizarre. Only recently did we standardize WebAssembly, an assembly language for the web, that lets browsers break ties with JavaScript entirely!

My point is that **almost no standardized software was made in its perfect form**. In order to maintain backwards compatibility while iterating towards a better future, icky bits of archaism may hang around longer than we like.

The realization that **software is incredibly human** has vastly improved my ability to synthesize new information and empathize with the way things are rather than the way they should be. The sooner you realize this, the clearer the picture becomes and the better of a software engineer you will be.