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
