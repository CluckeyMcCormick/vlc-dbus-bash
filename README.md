# vlc-dbus-bash
A collection of Bash scripts to control VLC Media Player over `dbus`.

NOTE: There are actually two repos for this project. One on
[GitLab](https://gitlab.com/CluckeyMcCormick/vlc-dbus-bash), since that's my
personal preference, and one on
[GitHub](https://github.com/CluckeyMcCormick/vlc-dbus-bash) since that has more
visibility. The GitLab repo is considered the main one, with the GitHub project
being a simple mirror.

# Background
I originally created this repo for personal use. I was working on a project
that more-or-less mapped physical button pushes to `bash` commands. I wanted to
use this to play music, and I was too lazy to make my own program, so I decided
to just call `bash` scripts to make VLC player do what I wanted.

Over time, though, I wanted finer and finer control over how the audio was
playing - which is just not really possible with VLC's command line options. VLC
does support control over `dbus`, but I've worked with `dbus` tangentially.
That, and VLC's `dbus` implementation was either
[insufficient](https://wiki.videolan.org/DBus-usage/) or [too deeply mired in
the technicalities of `dbus` to be useful](https://wiki.videolan.org/DBus-spec/)
. Or at least, it was when I started this. 

Anyway, after a lot of experimentation, I was able to control VLC using DBUS. It
ocurred to me that:

1. A project like this could improve my fluency with `dbus`.
1. Since there is little documentation for how to use VLC's `dbus` interface,
   espescially from the perspective of a `dbus` novice, a little `bash` script
   library would be of significant utility to anyone who tries to do something
   similar to what I'm doing.

# Requirements
This is still a personal project, so it does not target all Linux distros. It
was developed on Ubuntu 22.04.2 LTS. Not to say it *won't work* on other other
distros, it's just not guaranteed. Hopefully the comments I've left are enough
to allow anyone to do some custom trimming.

The scripts are all written in `bash`, and were specifically written against
Version 5.1.16. These scripts are not guaranteed to run in other shells.

The scripts rely heavily on the `dbus-send` command. I believe there are
alternative programs for sending `dbus` messages, but this is the one we use.

In addition, it's worth mentioning these scripts were developed for use in a
GUI/desktop environment. That means the scripts assume they are already running
in a `dbus` session of some kind. This will only really come up if, say, you're
trying to run the scripts from a `systemd` daemon of some kind.

# Install
Any scripts that you want to run on your machine from anywhere should be
installed (see: copied) to `~/.local/bin`. That should make them available on
your command line. Running the scripts directly would also work.

# DBUS Basics
If you're just trying to wrap your head around `dbus`, I figure I can impart
some basic knowledge to get you started. First off, I highly recommend you
download the `d-feet` program. This program allows you to inspect `dbus`
interfaces and makes understanding and visualizing your machine's `dbus`
environment **much** easier.

Anyway, there's a lot of documents out there that will cover the actual
programmatic structure of how `dbus` works. Personally, I recommend
[this one](https://dbus.freedesktop.org/doc/dbus-tutorial.html).
For this brief summary, we don't care about that - all we care about are the
external parts that we actually need to interface with.

So, at the top level, you have multiple **buses** - typically a single
*system bus* and multiple *session buses*. The different `dbus` interfaces are
spread across these buses. Each bus is specified by it's **address**. It's
possible to communicate across buses as long as you know those addresses.

The session buses are just what they sound like - it's the bus for your current
session. The scripts here exclusively use the current session bus. Since users
generally only have one login session going on, they generally only have one
session bus. Obviously there's ways to start new sessions, but that falls
outside what we need for this document.

Anyway, when programs connect to a `dbus` bus, the connection itself is given a
unique name. This name is referred to as the **Bus Name** - it's the name *on*
the bus, not the name *of* the bus. Programs can actually request their own
unique bus names, which typically take the form of
`org.[organization].[program name]`. For example, `org.pulseaudio.Server`.

The connection can then have a number of **objects** on it. Mostly I've seen
only one object per connection, but I have seen multiple objects before. Objects
are given an **Object Path** to identify them. They look like a file path, like
`/org/mozilla/firefox/Remote`.

Objects can then have a number of **interfaces** on them. Rather confusingly,
these are name similarly to the bus names, using a dot notation. It's not
unusual to see an interface name and a bus name that are almost exactly the
same, if not *exactly* the same. This may also appear very similar to the object
path. For example, one interface I inspected looked like so:

- Bus Name: `io.snapcraft.Store`
- Object Path: `/org/freedesktop/PackageKit`
- Interface: `org.freedesktop.PackageKit.Modify`

Finally, underneath interfaces, we have signals, methods, and properties. These
are the actual parts of DBUS that we can interact with.

Methods are effectively functions we can call and get output from.

Properties are akin to variables we can check and set.

Signals are a bit trickier. The best way to describe them is as messages that
can we can listen for.

Methods, properties, and signals are all addressed using dot notation. This is
added on to the interface name. As an example, the full path for the method
`Introspect` on `org.freedesktop.DBUS.Introspectable` would be
`org.freedesktop.DBUS.Introspectable.Introspect`.

It sort of a series of nesting dolls. The documentation I linked earlier renders
it like so:

```
Address -> [Bus Name] -> Path -> Interface -> Method
```

That surface level knowledge should be enough to understand what's happening in
these scripts.

