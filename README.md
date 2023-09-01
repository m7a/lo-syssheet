---
section: 11
x-masysma-name: syssheet
title: System Sheet Script
date: 2020/03/09 18:39:27
lang: en-US
author: ["Linux-Fan, Ma_Sys.ma (Ma_Sys.ma@web.de)"]
keywords: ["script", "mdvl", "syssheet"]
x-masysma-version: 1.0.0
x-masysma-repository: https://www.github.com/m7a/lo-syssheet
x-masysma-website: https://masysma.net/11/syssheet.xhtml
x-masysma-owned: 1
x-masysma-copyright: |
  Copyright (c) 2012-2020 Ma_Sys.ma.
  For further info send an e-mail to Ma_Sys.ma@web.de.
---
Name
====

`syssheet` -- System Sheet Script to display system information

Synposis
========

	syssheet [-g|-c|-f]

Description
===========

The MDVL System Sheet Script gives a one-screen system overview. It consists
of the following panes:

General Information
:   Displays the current user name, the terminal type the script is running on,
    kernel and architecture information, date and time, hostname, uptime,
    RAM usage and CPU types.
Network
:   Displays the physical interfaces, their addresses (if assigned) and the
    amount of data transmitted/received.
File systems
:   Displays a subset of the mountpoints visible by `df`. For each mountpoint,
    the amount of data used and total available space is displayed. If quota
    is installed and quota are set, the current quota usage is displayed
    below the file systems.
Users
:   Displays the top process in terms of memory and the top process in terms of
    CPU usage for each user

Options
=======

----  --------------------------------------------------------------------
`-g`  Draws the sheet using continuous lines instead of `-` and `+` signs.
`-c`  Colorizes the output
`-f`  Combines the effects of `-g` and `-c`
----  --------------------------------------------------------------------

Examples
========

~~~
$ syssheet
+------- System Sheet Script 1.2.1, Copyright (c) 2012-2020 Ma_Sys.ma ---------+
| linux-fan (id 1000) on rxvt-unicode-256color    Debian GNU/Linux 10 (buster) |
| Linux 4.19.0-8-amd64                                                  x86_64 |
| 09.03.2020 18:42:26                                                     pte5 |
| up  1:31,  8 users,  load avg: 0.10, 0.21, 0.15               1803/32121 MiB |
| 8 Intel(R) Xeon(R) CPU E3-1231 v3 @ 3.40GHz                                  |
+---------------------------------- Network -----------------------------------+
| Interface                      Sent/MiB  Received/MiB                Address |
| eno1                                  0             0                        |
| eno2                                198         10500        192.168.1.16/24 |
| ens2                                  0             0         192.168.2.1/30 |
+------------------------------- File systems ---------------------------------+
| Mountpoint                     Used/GiB        Of/GiB             Percentage |
| /                                   186           251                    76% |
| /fs/ll                             1225          1563                    80% |
| /fs/e01                            1415          1615                    93% |
| /data                                98           216                    48% |
+----------------------------------- Users ------------------------------------+
| Username     MEM/MiB            Top/MEM      CPU          Top/CPU   Time/min |
| linux-fan       1747        firefox-esr    13.4%             Xorg         10 |
| root             376            dockerd     0.4%  docker-containe          0 |
| www-data          69            apache2       0%          apache2          0 |
| colord            13             colord       0%           colord          0 |
+------------------------------------------------------------------------------+

$ syssheet -g
┌─────── System Sheet Script 1.2.1, Copyright (c) 2012-2020 Ma_Sys.ma ─────────┐
│ linux-fan (id 1000) on rxvt-unicode-256color    Debian GNU/Linux 10 (buster) │
│ Linux 4.19.0-8-amd64                                                  x86_64 │
│ 09.03.2020 18:41:57                                                     pte5 │
│ up  1:30,  8 users,  load avg: 0.16, 0.23, 0.16               1800/32121 MiB │
│ 8 Intel(R) Xeon(R) CPU E3-1231 v3 @ 3.40GHz                                  │
├────────────────────────────────── Network ───────────────────────────────────┤
│ Interface                      Sent/MiB  Received/MiB                Address │
│ eno1                                  0             0                        │
│ eno2                                198         10500        192.168.1.16/24 │
│ ens2                                  0             0         192.168.2.1/30 │
├─────────────────────────────── File systems ─────────────────────────────────┤
│ Mountpoint                     Used/GiB        Of/GiB             Percentage │
│ /                                   186           251                    76% │
│ /fs/ll                             1225          1563                    80% │
│ /fs/e01                            1415          1615                    93% │
│ /data                                98           216                    48% │
├─────────────────────────────────── Users ────────────────────────────────────┤
│ Username     MEM/MiB            Top/MEM      CPU          Top/CPU   Time/min │
│ linux-fan       1747        firefox-esr    13.9%             Xorg         10 │
│ root             376            dockerd     0.4%  docker-containe          0 │
│ www-data          69            apache2       0%          apache2          0 │
│ colord            13             colord       0%           colord          0 │
└──────────────────────────────────────────────────────────────────────────────┘
~~~

![Output of `syssheet -f` / Screenshot](syssheet_att/scr.png)

Rationale
=========

This script is intended to be usable as a script to start by default in a new
terminal. It makes sense for local, as well as remotely connected systems.

For local systems, it provides all basic troubleshooting information on a single
screen, for remote systems, it immediately gives an idea about the current
utilization of the system (e.g. other users performing compute-intensive tasks
etc.)

Syssheet is intended to be smaller and run faster compared to some alterantives
like `inxi` at the expensive of providing only a fixed set of information.
Data from mountpoints and network interfaces is presented in a way which
attempts to mainly include physical filesystems and network interfaces as not
to obsucre the view by container mountpoints and interfaces. Compare the outputs
of `ip link` and `mount` against the typical syssheet-report to see the
difference.

Integration with Bashrc and System
==================================

To build a Debian package for this script, invoke `ant package`. If no package
is needed, everything is included in file `syssheet` and thus in a suitable
form for copying to remote systems. The only noteworthy dependency is `gawk`.

You can add `syssheet` to your `bashrc` as described in the following.

Add the following at the begin of the file:

~~~{.bash}
[ -z "$PS1" ] && return # Terminate when not running interactively
export LINES
export COLUMNS
/usr/bin/syssheet -f &
sheetpid=$!
~~~

Add the following at the end of the file:

~~~{.bash}
( sleep 3 && kill $sheetpid ) 2>&1 > /dev/null & sleeppid=$!
disown
wait $sheetpid
kill -s TERM $sleeppid 2> /dev/null
unset sheetpid
unset sleeppid
echo
~~~

These snippets start a `syssheet` in the background and stop it, if it does not
complete within three seconds. The time limit is important to avoid hangs due
to slow mountpoints. Adding the snippets in the begin and end respectively,
allows all other bashrc parts to run in parallel to the syssheet.

Bugs
====

 * Running without `gawk` produces garbled output.
 * Terminal needs to be at least 80 characters wide.
 * Memory reports need to be considered approximations.
 * Performance improvements welcome.

See Also
========

[inxi(1)](https://manpages.debian.org/stable/inxi/inxi.1.en.html)
