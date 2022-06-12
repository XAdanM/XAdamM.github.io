---
title: "Writing Masochistic Websites"
date: 2022-06-11T22:07:14-05:00
draft: true
description: "Enjoying the pain of writing complex websites without JavaScript"
type: "post"
tags: ["web-development", "web", "websites", "programming", "html", "css", "javascript"]
---


So JavaScript sucks. Websites with JavaScript suck. The internet is flooding with articles about that, so I don't need to tell you that again.

But what I'm going to prove to you today is that writing websites without JavaScript sucks.


## Case study 1

Enter [exozy.me](https://exozy.me/). A perfectly simple, clean website. It's looked exactly like that for ages (actually, more like about a year, but whatever). What you don't see is the rewrites it's been through. Although that website has always been pure handwritten HTML/CSS stuff, I've snuck in a tad bit of JavaScript here and there to make *my* life easier. The poster child for this is the [join](https://exozy.me/join) page which now uses a simple HTML form. But back then, this used... a simple HTML form. Yes, but with spooky icky [JavaScript](https://git.exozy.me/exozyme/website/src/commit/375086c30bcad2258717d67e25457a47394333bb/join/script.js)!

So you're probably like, that seems like a very very reasonable use of JavaScript totally unlike the convoluted spagetti that you see in your typical website's view source after clicking on the JavaScript files actually responsible for the page's bloat since the HTML source is one freaking line. And yes, you're probably like, I'd totally enable my JavaScript to run that even if normally I don't allow a single line of JavaScript to run in my browser to avoid all of its spooky ickiness. But no, I wanted exozyme's website to be 100% JS clean since after all, it's a privacy-focused site, and JS is usually public enemy of privacy #1 in the browser.

So I ended up moving that JS logic to the backend, so now some slow bloated Python API is doing all those username and email checks with regex that no one can understand, instead of it being in the frontend.

I think that's actually the only place we used JS, since the rewrites were mostly for wrangling our wild CSS.


## Case study 2

OK, time for our second victim, [laduecs.club](https://laduecs.club/). Now this used to be a perfectly innocently [obese React site](https://codeberg.org/LadueCS/pages/src/branch/legacy), a criminal React site indeed since it's literally a freaking static webpage with zero interactivity. My sister's web dev class could remake that thing in vanilla HTML and CSS in a day (if they used enough StackOverflow of course). But no, we pull in 1000 dependencies and take up 500MB just to build some scraps of unreadable JS.

Alright, so let's fix it up! Now the situation in the last paragraph was actually even worse if you can believe it, since the old website wasn't the cool animated (but not interactive) thing you see now, but a cookie-cutter template super-standard cliche-to-the-max website. Seriously, you can checkout the old `github-pages` branch and see the crap in action yourself. (Yeah and this is why I'm not publishing this post publicly since the people that made that website are perfectly great people and don't deserve this roast. Also I don't like roasting myself. Actually, it's kinda fun since the victim can never punch you.)

So anyways, I got to work rewriting everything in plain old HTML/CSS and to spice things up, I went with an animated terminal theme. Now if you're a reasonable person, you would have told me to use JavaScript for the animation so I wouldn't have to die of CSS animation poisining. But no, I went ahead with doing it all in CSS, and as a result, the website looks amazing in Lynx and cost me a few billion brain cells in frustration.

Yeah, don't do what I did.

Just use JavaScript.

The CSS looks clean and less than 100 lines, but those were the worst 100 lines of my life.

Just use vanilla JS and don't do React kids, and you'll be fine.
