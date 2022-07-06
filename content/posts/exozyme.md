---
title: "exozyme"
date: 2021-11-29
draft: true
type: "post"
---


*Originally posted on [PCPartPicker](https://pcpartpicker.com/b/ftPnTW)*


Like many mistakes, this one was made a long time ago and continues to bite me to this day. One year ago, to be exact. OK, that's not *that* long ago, but whatever.

The mistake was not that I started a year-long project to build this PC.

Well obviously it wasn't, or else I wouldn't be celebrating my build's completition with a "Completed Build" on PCPartPicker.

The mistake was missing out on a great Cyber Monday laptop deal. It's not like my current laptop is terrible: it's a six-year-old ThinkPad Yoga 14 with a lovely touch screen and pen and runs perfectly and Linux distro that I throw at it. But throw Zoom at it and the fans start spinning wildly and it crashes and burns worse than running 20 benchmarks simulatenously. Why is Zoom too lazy to add graphical acceleration to their Linux app when I need it the most?

It would have been tolerable except the pandemic happened and ruined everything. I love this description that I wrote a few months ago:

> What a perfect time to build a PC: massive supply chain disruptions, cryptocurrencies driving up GPU prices through the roof, (scalper) bot wars, and a pesky little thing called the coronavirus murdering 2.5 million people (that last one probably sounds completely alien to anyone before 2019). Just perfect!

Suddenly everyone was using Zoom every day! I don't think there's an app that I hate more now than Zoom.

Anyways, many other things like virtual machines, compiling code, emulators for modern consoles like Cemu and Yuzu, developing machine learning programs, and Blender renders are similarly excruciating on this laptop. 

Now I could have gotten a powerful laptop. Or, for half the cost, a desktop with even more power. (Both computational power and electrical power, but electricity is decently cheap)

So thus began this build.

Five months later, it was finally functional! I even created a "Completed Build": https://pcpartpicker.com/b/GnV7YJ

The only problem was that it wasn't actually complete. Well, actually there were many problems. A cardboard box case doesn't exactly have the best thermals, and the GPU ran games worse than my laptop, assuming the game actually worked in the first place and didn't run into OpenGL compatibility issues. Well, you can't expect much from a GPU that was entry-level in 2011.

Alright, so what changed between that build and now? The first change was getting an actual case. The Corsair 4000D Airflow is a bit large for my taste but it fits all of the components spaciously, and it was easy and straightforward to move my build into the new case.

I also added a spare 2 TB hard disk drive to the build since the NVME was running out of space. The setup is pretty standard: The NVME stores Arch Linux, apps, games, databases, VM disks, and anything else that would otherwise take forever to load, and the HDD stores photos, videos, and backups.

You might also be wondering how I got a 5900X for $390... after taxes. (All the prices listed are after taxes) I managed to snap one that was on sale for $500. I was then able to get it down to only $375 since I know someone with an AMD employee discount. So there you go. I doubled my cores with only $60 more... not bad! OK, it was more than that since I lost some money selling the used 5600X on eBay, but it was still a great deal.

I first tried running the 5900X with the stock Wraith Stealth cooler and the average idle temperature was 80 degrees. That's the idle temperature. I also tried running a benchmark, after I pondered for a while on whether it would destroy the CPU, and the results were worse than the 5600X. So much for the upgrade... Anyways, I finally stopped procrastinating about buying a proper cooler, and the Scythe FUMA 2 works great since I don't intend to overclock. The CPU hits 4.9 GHZ all the time, and it works perfectly fine with the new cooler.

The same thing happened with the GPU. Over the summer, it looked like GPU prices were beginning to fall from their sky-high prices (more like distance from the Earth to the Moon-high prices), but it was only a mirage and prices went back up. Eventually, I couldn't tolerate the terrible GPU any longer and went to a local Micro Center and got scammed by a Micro Center employee to buy their most expensive 6600 XT model. Look, their website only had one model in stock so I didn't do much research on this. Fortunately, the AMD employee discount shaved off about $100 from the ludicrous price, so it was worth it... kind of. I would have gone with a card that balanced the CPU better, but the 6600 XT was expensive enough.

I also upgrade the RAM to 32 GB to avoid triggering the fearsome Linux OOM killer any time I do something memory-intensive.

Lastly, I bought an M.2 Wi-Fi and Bluetooth card, and it fits snuggly in the case, under the GPU, and works perfectly. No complaints here.

So what's all of this stuff being used for?

A lot, actually. This PC is a desktop, workstation, server, home entertainment system, and more. It hosts https://exozy.me/ and plenty of cool stuff, including remote desktop, Nextcloud, Synapse, Gitea, Jellyfin, Mastodon, PeerTube, and JupyterHub for around 50 users. With such awesome specs, the main limitation is my internet's upload speed! All of the services are running bare-metal instead of using Docker like in a typical homelab, since yes, the AUR really is that good. For these services, I usually leave the PC on all day (gotta get that 100% uptime), and it uses around 40 watts, which isn't great but tolerable.

It's also a blast to write code on this PC. VSCode is actually fast, compared to its normally sluggish behavior on my laptop, and Vim is blazingly fast. Compile times are similarly amazing: around 15 times faster than my laptop.

The PC is also (sometimes) hooked up to an AV receiver for watching movies or gaming at 4K 60 Hz. Usually the GPU can't hit 60 FPS at this resolution, but I can't really tell the difference anyways. (Yes, I know https://frames-per-second.appspot.com/ exists)

This PC really can do anything. It figuratively feels like literally finishing a marathon to complete this build after one year of planning, waiting, and worrying about the pandemic. Well, it looks like this really is the end, both of this build description and of this project. What a journey. Now time to enjoy some gaming on the completed build!

