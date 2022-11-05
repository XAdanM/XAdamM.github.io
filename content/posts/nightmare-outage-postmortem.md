---
title: "Nightmare Outage Postmortem"
date: 2022-11-05T19:51:33Z
draft: true
description: "Why you should never use Arch Linux on a server"
type: "post"
tags: ["linux", "arch", "server", "pain", "postmortem"]
---


A few weeks ago, my friend asked me about the worst I've ever borked a Linux system. I happily answered that I've never really messed up badly, and the worst I've done is deleted my disk partition table while trying to install GNU Guix, which was easily repairable.

That all changed yesterday.

There are some mistakes that can really bork your system. But string together several of those mistakes, and that's the situation I was in.

So I could blame my incompetence for all the mistakes I made, but none of this would have happened if OpenSSL (AKA CVESSL) wasn't incompetent. Arch Linux took this opportunity to upgrade OpenSSL from version 1.1 to 3.0, and there began my pain.

OpenSSL, as terrible as it is, is a dependency of basically everything, so the Arch maintainers had to rebuild and upload a ton of packages. I happened to update the exozyme server during the middle of this process, so I ended up with a system with OpenSSL 1.1 installed but lots of packages throwing errors about `libcrypto.so.3` and stuff.

So, being the genius I am, I ran `paru -U https://archive.archlinux.org/packages/o/openssl/openssl-3.0.7-2-x86_64.pkg.tar.zst`. (Basically it force downloads the newest version and installs it without regard to whether this would break my system or not)

Yeah, my brain probably wasn't functioning at all at the time, since this just turns those `libcrypto.so.3` errors into `libcrypto.so.1` errors.

Oh yeah, and systemd still depends on OpenSSL, so one reboot later, welcome to kernel panic land!

The best part was that the exozyme server is in another city, so I couldn't even see systemd triggering a kernel panic and spent 15 minutes wondering why exozyme was taking so long to boot.

So, I got into a Jitsi Meet with some people with physical access to the server, thinking we could just boot it into recovery mode, reinstall OpenSSL 1.1, and get the server back up and running!

Unfortunately, we couldn't get into the systemd-boot boot menu, despite trying spamming three different keys.

We couldn't get into the BIOS menu, despite trying spamming three different keys.

I then remembered that I enabled an evil setting called "ultra fast boot" in the BIOS a year ago that basically causes the boot to be so fast you can't even get into the BIOS menu by spamming a key, because yay boot speed! And exozyme will never fail to boot, right? 

Imagine trying to coordinate another person to reset a motherboard CMOS over a low-res video call. Yeah, that's called torture.

After an uncountable amount of pain, we finally got the CMOS reset so that the "ultra fast boot" setting would be reset back to disabled, and then unlocked access to the BIOS menu.

Even better, we got more bang out of our buck and could now access the boot menu too!

There's an old Linux trick to boot a borked system: add the `init=/bin/bash` boot parameter. As expected, this worked perfectly, except it didn't. Our USB keyboard simply could not type anything once exozyme booted up.

Alright, let's try `systemd.unit=emergency.target`! systemd says nope and throws us a big ugly kernel panic. Same result with `systemd.unit=rescue.target`.

That leaves us with the method of last resort: creating a bootable Arch USB and booting that and `chroot`ing to the seriously borked system.

That method should have taken 20 minutes, but it ended up taking 2 hours because `pacman` installed a bunch of empty files for the sole purpose of making me more frustrated.

Interestingly, my Arch laptop survived this OpenSSL update perfectly fine (which is one of the reasons I was so reckless with updating OpenSSL on exozyme), so I'm hesitant to blame Arch Linux for this obvious instability. I love blaming OpenSSL for totally random things though.

As you can see, running an Arch Linux server is quite a lot of pain. Running self-hosted services on top of said Arch Linux server is maximal pain. This experience has seriously made me question whether exozyme can continue as is, and I'm actually not a huge fan of self-hosting either (being a sysadmin is pure pain), but the alternatives are not great. I don't have the time needed to properly maintain exozyme, so it's more likely that I'll find another person to help with maintainence.

And to put the cherry on top of this terrible day, my trusty backpack that survived 7 years of abuse also broke that day, so I think my luck was just rigged or something.

