---
title: "The Coolest exozyme Tricks You've Never Heard Of"
date: 2022-07-29T13:15:23-05:00
description: "Some interesting and obscure things you can do with exozyme that have virtually zero documentation"
type: "post"
tags: ["exozyme", "linux", "tricks"]
---


## 1. exozyme wiki

OK fine, if you're an [exozyme](https://exozy.me) member, you've probably at least heard that we have a [wiki](https://git.exozy.me/exozyme/exozyme/wiki/). It's documented at several places on our website too. But is it any good?

Kinda... it has quite a few articles, but many of them are very short since the wiki is entirely community maintained. And actually, that's the whole reason I wrote this blog post today, since we need better documentation for these obscure tricks!

## 2. exoci

We host [Woodpecker CI](https://woodpecker-ci.org), and it's amazing. You probably haven't used it, so let me pitch it to you: one second build times. I'm serious, builds are insanely fast! Proof: [my website's builds](https://ci.exozy.me/Ta180m/website) are so fast Woodpecker lists them as "not started yet" since they finished so quickly!

It's due to two factors: our wonderful 24-thread Ryzen 5900X CPU, and a ton of caching. We use the Woodpecker local backend which runs commands on the server directly, not inside a container. (We actually wrote the local backend ourselves and contributed it upstream to Woodpecker!) Then, instead of cloning the repo every time and rebuilding completely from scratch, since the CI is run directly on the server we can cache the repo and build.

If you want to try this out yourself, shoot us a message on Matrix and we'll enable CI access for you. The CI has some security issues 😬 so new users don't have access by default. There's an incredibly short wiki article about the [CI](https://git.exozy.me/exozyme/exozyme/wiki/CI) which probably won't be helpful, but it's there for your reference.

## 3. scp pastebin

If you `scp` a file to the directory `/srv/http/pages/bin` on exozyme, the file will magically appear at `https://bin.exozy.me/filename`, thanks to some [nginx magic](https://git.exozy.me/exozyme/nginx/src/branch/main/pages.conf#L25).

## 4. GPU computing

We have a Radeon 6600XT graphics card, which is a pretty underwhelming GPU overall, but you can use it for GPU computing anyways. Check out the very short [wiki article](https://git.exozy.me/exozyme/exozyme/wiki/GPGPU) for more information

## 5. Host dynamic websites on Unix sockets

You can host dynamic websites on exozyme by starting a world-readable Unix socket in `/srv/http/pages`. Again, due to nginx magic, your dynamic website will magically appear at `https://filename.exozy.me`!

## 6. Browse exozyme files using SFTP

In your file manager on your local machine, enter `sftp://username@exozy.me`. Your files will magically appear! (Assuming you already have [SSH](https://git.exozy.me/exozyme/exozyme/wiki/SSH) set up)

## 7. Host applications on exozyme

If you want to use exozyme to host your application, enable lingering so your application runs when you're not logged in with `loginctl enable-linger $USER`. Then write a systemd user service and enable and start it!

## 8. Bridge Matrix and Discord

Matrix is great, but you probably haven't been able to convince your friends to switch to it. Don't worry, because we host a [Matrix and Discord bridge](https://git.exozy.me/exozyme/exozyme/wiki/Bridge-Discord-and-Matrix). Check out that article for how to get started.

## 9. Run GUI apps in code-server

[code-server](https://github.com/coder/code-server) is a web editor that we host on exozyme. Sometimes, you need to quickly run a GUI app, but it's a web editor!

However, if you connect to exozyme with `waypipe ssh exozy.me`, you can tunnel GUI apps over SSH using [Waypipe](https://gitlab.freedesktop.org/mstoeckl/waypipe). (This requires Wayland on your side). Now start `code-server` and try running GUI apps inside the code-server terminal. The performance probably won't be great, but it works!

## 10. Custom desktop environments

This one is insanely cool. Thanks to [Distrobox](https://git.exozy.me/exozyme/exozyme/wiki/Distrobox), you can easily create a Podman container that's integrated with the rest of exozyme. Now install a desktop environment inside the container, then edit your `~/.xinitrc` to `distrobox enter -- dbus-run-session gnome-session` or whatever DE you want to start. Connect to exozy.me with a remote desktop client, and boom, you now have a custom environment where you have root that you can customize however you want. See [this post](https://social.exozy.me/@ta180m/108728318919353742) for some screenshots!
