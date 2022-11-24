---
title: "Laptop Necromancy"
date: 2022-11-24T21:21:40Z
description: "Reviving a trash laptop from 2012"
type: "post"
tags: ["laptop", "e-waste", "linux", "retro", "hyperbole", "humor"]
---


I don't think my blog has any regular readers, but if you are, you're about to be disappointed. After a three-month drought of posts, I'm finally back, with some boring stuff.

OK, it's not that boring I guess, but I'm mostly just blogging for the sake of blogging, not because I have anything interesting to say.

That aside, let's get started with the story.


## Trash

How often do you replace your smartphone? Everyone knows that people just throw away perfectly good electronics, which makes me wonder what kind of supercomputing cluster you could build out of all that e-waste (Maybe you could crack [RSA-1024](https://en.wikipedia.org/wiki/RSA_numbers#RSA-1024)). Even at MIT, people throw out mounds of electronics: literally mounds! In the basement of building 32 (In Unix, everything is a file; at MIT, everything is a number), there's a e-waste disposal site with huge piles of discarded electronics. Sadly, the electronics are all haphazardly dumped there so they've suffered physical damage, but there's still plenty of functional trash. There's even a former biohazard-storage refrigerator, which has a sticker on it assuring me it's been properly decontaminated, but I'm not sure how I'd haul it to my room. (I'd totally try storing food in it. I'd like to see if the food turns green and mutant.)

Anyways, here's a breakdown of what's typically at this disposal site. The people who throw away perfectly good electronics, garbage trucks, and scavengers are roughly at equilibrium, so there's always the same amount of e-waste, just a different set of it every day.

- Printers: Everyone hates printers, so it's no surprise there are always a ton of printers and printer parts there. Imagine dragging one back to your dorm room in the freezing cold of November.

- Monitors: For some reason, the monitors are often missing parts, so it's normal to just see a monitor stand without the display.

- PCs: Scavengers target PCs quite a lot, so they're always missing the CPU. And sometimes the RAM sticks. Maybe some people just collect old CPUs or something.

- Peripherals: Somewhat rare, because keyboards and mice easily fit inside backpacks, so the logistics of scavenging them is trivial.

- Laptops: I'm from Missouri, and we allegedly have red foxes here (I swear this is relevant, just wait). Now red foxes have cult following for some reason, and although I don't subscribe to that, I've still wanted to see one in person. There's not really any trick for spotting a fox, other than being at the right place at the right time, or going to a zoo. But wait long enough, and it will happen. I'm not so optimistic about my chances of finding a laptop at this disposal site, for the simple reason that I've seen a red fox twice and I've seen zero laptops at the disposal site, maybe because they get scavenged instantly.

![An example of e-waste at the disposal site](/img/ewaste.webp)


## Winning the lottery

I've never won the lottery. But I have a friend that has.

The lottery in question here is of course finding a thrown-away laptop in the basement of building 32.

It wasn't just any laptop. It was a Dell XPS! Specifically, a Dell XPS 13 L321X from 2012. And they even found the charger! And there wasn't any physical damage! And even better, the laptop started up when they plugged in the charger! And then it amazingly booted up Ubuntu just fine!

We had a lot of fun decorating the outside of the laptop with weird stickers:

![Stickers!](/img/stickers.webp)

The only thing missing was the SSD, which was presumably securely wiped and discarded elsewhere. Due to this, my friend had trouble installing Ubuntu, because it was impossible, so they asked me for help. I bought a [$20 replacement SSD](https://www.amazon.com/LEVEN-mSATA-256GB-30x50-9mm-Internal/dp/B08M64B83G), and once it arrived, I booted up Ubuntu again—except Ubuntu wouldn't boot up.


## I hate you BIOS

Legacy software sucks. This laptop's BIOS made all other legacy software look brilliantly designed. When I tried booting up my Ubuntu 22.10 USB stick, it spat at me the error message "Operation system not found". Yes, this is not a typo.

After checking the usual suspects, I determined it was because my friend had tried a Ubuntu 20.04 stick, and in the versions between these two, Ubuntu had messed up their live ISOs for legacy BIOS boot systems, and basically required UEFI.

Not a problem. [Ventoy](https://www.ventoy.net/) to the rescue! If you've never heard of Ventoy, it's an amazing software for your USB sticks to make them boot up anywhere and you can drag and drop ISOs to a special folder on your USB stick and Ventoy will boot them. No need for `dd` or BalenaEtcher.

Ventoy happily booted up a Ubuntu 22.10 ISO, and I started the installation. It went perfectly and was suprisingly fast. One reboot later, and the laptop BIOS mocked me again with "Operation system not found".

Rip. OK, I give up on Ubuntu 22.10. It obviously doesn't want to cooperate. What next? Install Ubuntu 20.04 and do two `do-release-upgrade`s to get to 22.10? Nah, let's read terrible clickbate articles about "the best Linux distros for 2022 you should try right now" and find something else.

I decided to give Pop!_OS a try, mainly because it seemed rebellious to include special characters in your distro name so that everyone spells it incorrectly. Pop!_OS booted up fine without any need for Ventoy hacks, so I thought this installation would work.

Nope. "Operation system not found" again. Ugggghhh!

Stupidly, I tried installing Pop!_OS again, and guess what? It didn't work.

I even tried `dd`ing the Pop!_OS ISO to the SSD and booting up that, which did actually work, so at least the new SSD itself was fine and I hadn't just wasted $20.

This whole time, I was mindlessly searching the web for things like "dell xps 13 l321x uefi" and "convert gpt to mbr linux". Of course, none of that helped, so I tried opening GParted in the live ISO session and taking a look at what horrors the Pop!_OS installer did to my disk. Turns out, it made a total of *two* partitions: a root parition and a swap partition. Uh oh, that doesn't look good. ArchWiki assured me that this was actually normal, but I wasn't so sure. Stupidly, I tried setting the `boot` flag on the first partition, since it did contain the `/boot` folder inside there along with the other usual suspects under `/` such as `/etc` and `/usr`.

I rebooted for the nth time, and prepared to boot up the live USB again, since this obviously wouldn't work.

It did.

I probably should have been ecstatic at that moment, but I instead felt an insane amount of hatred for that terrible BIOS. Nowhere does it say anywhere on the internet that you need to set a partition as bootable to trick the brain-dead BIOS into thinking there's an "operation system" on the disk. The partition isn't even actually bootable! But anyways, this trick works, so now there's at least one place where it's documented now.

![Pop!_OS finally working!](/img/popos.webp)

Anyways, Pop!_OS works perfectly now, but I want my afternoon back, BIOS!

