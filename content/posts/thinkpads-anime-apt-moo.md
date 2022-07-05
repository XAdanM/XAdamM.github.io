---
title: "ThinkPads, Anime, and apt moo"
date: 2022-07-05T18:04:13-05:00
draft: true
description: "Drawing when you don't know how to draw"
type: "post"
tags: ["drawing", "art", "thinkpad", "anime", "life", "learning"]
---


It all started with that [Wacom tablet](/posts/putting-wacom-tablet-good-use/) two years ago... (Well more accurately, it all started when the Big Bang happened, but that would unnecessarily lengthen this post. By the way, I should take a picture of the toy llama that inspired the drawing in that previous post, since it's as horrendous as the drawing suggests.)

Well anyways, I've wanted to actually do something useful with the Wacom tablet and Krita, since:

1. The Wacom tablet is a Cintiq 16, which is apparently a really sought-after model? I used to think it was $60 since it's literally just a screen and a stylus, but it's actually around $600, which makes me regret dropping it all the time...

2. I got a [new laptop](/posts/laptopia/), so I no longer have to worry about Krita draining 100% CPU on my old ThinkPad's two cores. Now with the successor ThinkPad, I can set its power profile to performance, and watch the cores hit 97°C and do all sorts of crazy stuff.

So back in May, even before I got the new laptop, I started a Krita project that I thought would take two days. The mission was simple. Draw an anime girl mascot for [Ladue Computer Science](https://laduecs.club/).

There was only one problem, and it was obvious. I don't actually know how to draw or use Krita, so yeah, rough times ahead for me!

I found a few good reference images online and started the project. The first day went by shockingly smoothly. My progress was slower than I'd hoped for, but by the end of the first day I had a satisfactory sketch and part of the line art done.

The trouble really hit on the second day. Rereading that older blog post about Krita, I can say that I no longer agree with any of the five points I listed.

1. Krita works perfectly fine on the new ThinkPad's screen, since I stupidly got a 4K screen.

2. The learning curve is meh. I had to check Krita docs a lot at first.

3. It's fast. Although in the beginning of the project, Krita was sluggish due to the old ThinkPad, with the new one it runs splendidly, even with an image with dimensions four times larger than anything I'd ever tried on the old ThinkPad.

4. Shading is not hard. Shading is basically impossible, since I don't know how to draw.

5. Layers can get pretty annoying sometimes. Towards the end of the project, my drawing had a complex hierarchy of layers and alpha inheritance and it became difficult sometimes to recognize which brush strokes were on which layer.

Now the fourth point is the one that I really want to talk about. I don't know how to draw, period, but I guess I'm still competent enough to draw a line art based on a reference image. I'm not the worst ever at drawing, since I know some people that can't even draw stick figures, but I'm at most average. I don't have any artisitc talent, yet at the end of the day I had a decent line art.

Shading is a whole different beast. Even with a reference image, I often just couldn't replicate the shading on my drawing. The most difficult part to shade was undeniably the hair. I probably spent a week stuck on how to shade the hair just like my reference images, which sent me searching through brush after brush and tweaking pressure curves, and totally ruining brushes via the brush editor, and arrrrghhh!

Now would probably be a good time to point out that when I said I spent a week on figuring out how to shade hair, I didn't actually spend 168 hours on it. I've been working on this project on and off, maybe spending a bit of time on it every few days, for two months. According to Krita, I've spent around 40 hours total on the project, which is a lot considering a speed paint of my drawing by a pro would probably take less than 2 hours, but I started out knowing absolutely nothing about drawing except how to draw horrid llamas.

For those two months, I've probably spent more time working on Gitea federation than this drawing. But I'm still glad I did spend time on this project, since I would probably have burned myself out working on federation if I hadn't taken breaks sometimes to draw.

Honestly, this project was challenging not because I can't draw, but because it was so long. For me, it's hard to persistantly work on something for several months, with no sign of the end. I had to completely restart last month, because I wanted to make major changes to the anime girl's pose and face shape, and you can't really change that unless you tear everything down and start afresh. I have an insanely low tolerance for repetition, so I almost wanted to just give up instead of redoing everything.

I also am very particular sometimes about details, so I would often spend a long time obsessing over small things and getting something perfectly correct, which slowed me down a lot.

For instance, I was having a lot of trouble picking a skin color for the drawing that didn't make the anime girl look like an extraterrestrial, so I sampled a color from one of the reference images. However, the sampled color made the anime girl look like an extraterrestrial, which was really problematic since it wasn't better than anything I could do myself. I even thought Krita was sampling the color wrong, so I spent an hour doing various experiements such as comparing the original color and sampled color side-by-side, an ultimately was sadly convinced Krita does indeed sample colors correctly.

I instead focused on drawing other parts of the project while I was stumped about the skin color. When I finally went back to the skin color, I just decided, screw it, I don't want to waste any more time about this, let's just use this bad-looking color. Lo and behold, once I finished the shading on top of the face, the color actually looked good now! I like to call these situations "trust me bro" moments, since I just didn't have any trust in myself or Krita or the reference image that things were going to be OK in the end.

Now the most fun part of the project was drawing the ThinkPad that the anime girl is holding, because she obviously needs a ThinkPad. I went with [these evil stickers](https://github.com/mkrl/misbrands) for the laptop stickers. I know absolutely nothing about perspective, so I just tried making parallel lines parallel, and hey, I think it looks good! Maybe if you actually know perspective, there's plenty of stuff to critique, but it lgtm. That's all that matters really. Also, if you squint, the ThinkPad has a Command key (evil, I know) since the best picture of a keyboard I could find was a Mac keyboard. I didn't want to draw a keyboard myself since it would look like a horrible accident happened to the keyboard, so I shamelessly stole an image of one off the internet and slapped it on to my drawing. Problem solved.

This whole project has basically been a giant exercise in problem solving. As a person with a strong math and science background, I approached drawing from a very systematic and logical perspective, which I'm assuming is what you're not supposed to do in art and instead let your emotions carry you or some nonsense like that, but I haven't encountered a problem yet in this project that I couldn't solve with enough hard thinking and experimentation. Hmmm, maybe *Gödel, Escher, Bach* is right when it comes to its claims about art...

The final fun problem I ran into was making the background. I'd always had an image in my mind of what I wanted the background to look like (despite my slight aphantasia), and I'd even asked some other LadueCS members a while back ago to help gather a [list](https://codeberg.org/LadueCS/pages/src/branch/main/img/mascot/background.txt) of funny Linux and Vim commands and programming language tricks to put in the background. Now when it actually came time to make the background, [cool-retro-term](https://github.com/Swordfish90/cool-retro-term) had the perfect eye-candy to make it happen. Unfortunately, since my canvas was 4961x7016 (A4 at 600dpi in case you were wondering), I also wanted the background to be that resolution since I care about details like that. That meant resizing a window of cool-retro-term to that size and taking a screenshot. Now I don't know about you, but I don't have any displays of that size.

My solution (it sounds incredibly dumb, but it works) was to first set my display scaling to 100% so I could fit a half-size 2481x3508 window on my 4K display (using Kwin window rules to hide the top border and resize the window to exactly the right size) and set the font size so the text (I used `vi` instead of `cat` for printing the text so we can have a nice `vi` easter egg at the bottom of the background) would snugly fit the window, and then set the display scaling to 200%. That caused the window to now be about four times as big as my display, but I had already fine-tuned the font size so the text would fit nicely, and all that was left was to take a screenshot of the giant window. The [screenshot](https://codeberg.org/LadueCS/pages/src/branch/main/img/mascot/background.png) came out to be around 20MB, which makes sense since it was basically an 8K image. Kinda. Square root of two instead of 16 over 9, same thing.

Now you're probably waiting to actually see the finished drawing since you probably want to judge the artwork of someone who can't draw.

So...

It actually isn't done yet, since there are still a few details left to shade, and I'm very picky about some details so I'm probably going to have to spend another few hours on tweaking minor things. Wait until tomorrow and I assure you it'll be done. Stay tuned.

Oh also, you can find the repo with all the stuff and resources I used for the drawing on [Codeberg](https://codeberg.org/LadueCS/pages/src/branch/main/img/mascot). I don't commit the actual `.kra` file very often since it's very very big (around 100 to 200 MB), but all the other stuff is up-to-date. [#GiveUpGithub](https://sfconservancy.org/GiveUpGitHub/)! And yeah, I probably should now work on Gitea federation, since that project has an incredibly low [bus factor](https://en.wikipedia.org/wiki/Bus_factor) that happens to include me...

