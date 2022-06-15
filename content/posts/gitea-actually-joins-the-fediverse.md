---
title: "Gitea Actually Joins the Fediverse"
date: 2022-06-14T21:57:29-05:00
description: "Gitea federation development has reached a crucial milestone: federating with Mastodon!"
type: "post"
tags: ["git", "gitea", "federation", "activitypub", "fediverse", "mastodon"]
---


So I don't know if you remember this or not, but on the first anniversary of the January 6 US Capitol riots, [the Gitea Mastodon account made an announcement on the fediverse](https://social.gitea.io/@gitea/107576791626052697) that changed my life. (Yeah, I know the January 6 riots have nothing to do with Gitea, but with all the news about the January 6 investigation lately, that's what I think of instantly when I hear that date) Gitea was joining the fediverse! 🎉

When I heard the news, the first thing I did was check the Gitea releases page for the latest release with all these cool federation features that they announced... and then I realized the announcement was not "Gitea joins the fediverse!" but rather "Gitea is joining the fediverse!" So to say the least, I was quite bummed out, since we really need a libre and decentralized alternative to GitHub (just look at youtube-dl).

So, curious to learn more about Gitea federation, I joined the [forge federation Matrix room](https://matrix.to/#/#forgefederation:matrix.batsense.net) and mostly just lurked in the background for two months and tried to learn Go and ActivityPub so I could contribute to the effort.

In March, I asked in the chatroom about how I could help, and [Loïc Dachary](https://blog.dachary.org/) suggested that I create a [pull request for Gitea](https://github.com/go-gitea/gitea/pull/19133) using some of the ForgeFriends code he had wrote. Working on this PR gave me invaluable hands-on experience with ActivityPub and the [Go-Fed](https://go-fed.org/) library, and I eventually managed to hack enough code together to do [federated Gitea following](https://social.exozy.me/@ta180m/108131455903841744).

Now that was insane! My Mastodon account is normally pretty low-traffic, but one of those posts in that thread got 20 reposts and 40 stars!

Even though this was an important milestone, things weren't that great under the surface though. The code quality? 🤢 No integration tests, no UI for federated following, and best of all, federated following only worked for users on the same instance, which kind of defeats the point of federated following. Don't even think about federating with Mastodon...

An even bigger obstacle loomed ahead though. [Go-Fed] is *quite* the complicated library and its binary size comes out to be a whooping 20MB, or a quarter of Gitea's current binary size. So yeah, Go-Fed isn't the nicest or leanest library out there, and we couldn't go on with using it for Gitea (although other projects like Owncast and WriteFreely have went ahead and stomached the extra 20MB).

During May, I was also busy with other things, so my Gitea federation work went on hiatus for a while... until we realized that there's actually another Go ActivityPub library, [go-ap](https://github.com/go-ap) out there mostly under the radar! It's at a much earlier stage of development than Go-Fed, but manages to squeeze the same functionality in 5% the binary size of Go-Fed. Yeah, that's right, 5%!

This month, I've mostly finished converting my old Go-Fed code to use go-ap instead, and time to revisit federated following!

This time, I wanted to do things right, and that meant federating with Mastodon to check if Gitea's ActivityPub implementation is correct.

Federating Gitea with itself is easy. Federating Gitea with Mastodon is super hard.

That's because if you don't speak ActivityPub correctly to Mastodon, it'll just refuse to federate with you. Also, Mastodon isn't very friendly about printing out useful debugging information even with the logging level set to `debug`, so I had to resort to some fun `sudo -e /var/lib/mastodon/app/controllers/concerns/signature_verification.rb` to jam tons of print statements into the source code of a live Mastodon instance, and then `sudo systemctl reload mastodon-web` to make it use the edited code.

After a ton of inserting print statements into random places, I finally got Mastodon to render the remote Gitea user. Actually getting Gitea to process Mastodon's incoming `Follow` Activity, create a remote federated user in the Gitea database, and send back an `Accept` Activity correctly took another day of debugging.

Almost all ActivityPub implementations use HTTP signatures so you know that incoming Activities are legit and not spoofed by some teen hacker in their mom's basement, so I had the bizarre issue that Gitea liked its own HTTP signatures but rejected Mastodon's and Mastodon liked its own HTTP signatures but rejected Gitea's. And it was a massive pain to debug, since... well here's a challenge for you: figure out what hs2019 is only by using a search engine.

Very long story very short, Mastodon was rejecting Gitea's signatures because Mastodon wants dates in GMT instead of UTC and silently fails otherwise (unless you throw in a gazillion print statements like I did), and Gitea was rejecting Mastodon's signatures because nginx was rewriting the Host header when reverse-proxying. It sounds like two tiny details, but that took way too long to debug.

So again, [another one of my fediverse posts](https://social.exozy.me/@ta180m/108472185098129371) got a whole bunch of reposts and likes, but I think it's actually deserved this time (and not the first time 😃). Why? Because Gitea has actually, seriously, really joined the fediverse! You can view my Gitea account rendered on your Mastodon instance, you can follow it, and you'll soon even be able to receive notifications when I do a Git push!

This isn't some hacky incomplete highly-limited implementation using a bloated Go ActivityPub library that desperately needs to go on diet. This is the real thing.

So what now? Federated pull requests when?

The good news is that this latest milestone overcomes many of the hurdles Gitea federation development was previously facing. Remote federated users, handling inboxes and outboxes, Mastodon interoperability, and don't forget the [Go-Fed binary size issue](https://gitea.com/gitea/go-fed-activity/issues/1) that stalled federation work for a month. After using go-ap successfully, there's no way I'd go back to Go-Fed.

There isn't any bad news!

The neither good nor bad news is that there is still plenty left to do, such as creating a UI for federated features, handling remote repositories, and actually implementing [ForgeFed](https://forgefed.org/) (all of this is still plain ActivityPub currently). My next target will be federated repo starring because it'll require ForgeFed and handling remote repos.

Anyways, after federated starring and the remote interactions UI are implemented, I think it's safe to say that Gitea federation will be 50% done. Of course, there will still be mounds of work left to do to handle all the different federated features we want Gitea to have, but the most difficult tasks will be finished. Reaching this current milestone has not required much code ("only" 1139 new lines) but a massive amount of thinking and discussing how to incrementally implement all the building blocks for federation.

You can read more about federated following at this [draft PR](https://gitea.com/Ta180m/gitea/pulls/6).

And since I get asked this a ton, these federated features like federated following are (obviously) not in Gitea right now, but will probably be released in Gitea 1.18 in October. Only four more months of waiting!
