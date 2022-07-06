---
title: "Reducing Bloat"
date: 2021-07-18T17:12:00-05:00
draft: true
type: "post"
---


*Originally posted on [ArchWiki](https://wiki.archlinux.org/title/User:Ta180m/Reducing_bloat)*


If there's one thing that hardcore Linux users are ridiculously obsessed about, it's the vague and scary concept of *bloat*. You gotta keep package counts low, use WMs, and compile stuff. What exactly is bloat? Wikipedia offers a few definitions, such as [https://en.wikipedia.org/wiki/Code_bloat code bloat] and [https://en.wikipedia.org/wiki/Software_bloat software bloat], but we aren't going to worry to much about it. The important thing to remember is that bloat is bloated and must be disposed of.

== Keeping package counts low ==

Use {{ic|pacman -S ''package'' --asdeps}} when installing a package that you don't plan on keeping, so that the next time you [[Pacman/Tips_and_tricks#Removing_unused_packages_(orphans)|clean up orphans]], the package will be automatically removed in case you forget to do it manually.

You can use {{ic|pacman -Qe {{!}} awk '{print $1}' {{!}} xargs -n1 pactree -cr}} to see which packages can have their [[Pacman#Installation_reason/installation reason]] changed to "as dependency". Note that this command depends on {{Pkg|pacman-contrib}}, which you can install with the method above. This will reduce the number of explicitly installed packages on your system.

Instead of installing {{Pkg|base}}, you can install only the packages that you need. Be careful about breaking your system if you do this.

You can find which of your packages have the most dependencies using {{ic|pacman -Qe {{!}} awk '{print $1}' {{!}} xargs -I {} -n1 bash -c "echo -n {}' '; pactree -c {} {{!}} wc -l" {{!}} sort -k2 -n}}. That should give you a good idea on what's most effective to uninstall to reduce your package count.

== Don't use a DE ==

See [[WM]] for a list of window managers you can use instead. Or even better, you can give up having a WM altogether and run your graphical apps from a TTY with {{ic|xinit ''application'' $* -- :1}}.

Another alternative is to use the window manager from an existing DE. For example, to run KWin standalone, use

 $ export $(dbus-launch)
 $ export QT_QPA_PLATFORM=wayland
 $ kwin_wayland konsole --xwayland

== Try not to install software outside of pacman ==

Never run any other package manager (pip, npm, gem, etc) as root. Always {{ic|make install}} to local directories unless you do not have any other options. Prefer the [[AUR]] to [[snap]] or [[flatpak]], but make sure you always read the {{ic|PKGBUILD}}.

