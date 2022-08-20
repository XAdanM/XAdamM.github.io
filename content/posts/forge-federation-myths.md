---
title: "Forge Federation Myths"
date: 2022-08-20T13:08:06-05:00
description: "Debunking common myths and misconceptions about forge federation"
type: "post"
tags: ["federation", "gitea", "myth", "git", "forge", "forgefed", "activitypub"]
---


Since I haven't blogged about forge and Gitea federation in a while, I thought it would be nice to tear down some strawman misconceptions about forge federation.

So first of all, if you're one of those people that thinks Git and GitHub are the same thing, please exit the premises now. Alright, got that out of the way, now on to the real deal.

## Git is already decentralized

If that's true, then why does everyone use GitHub?

Git is amazing, but it's really just a distributed filesystem. There's no access control, native issue tracking, project boards, CI, wikis, and so on. There's no way to only use Git and nothing else to collaboratively develop software. Even the kernel developers use Git along with email for submitting patches!

While there are a lot of projects like [git-dit](https://github.com/neithernut/git-dit), [git-issue](https://github.com/dspinellis/git-issue), and [git-appraise](https://github.com/google/git-appraise) that implement issue tracking use the Git distributed fileystem, they don't solve the problem of access control. To decentralize *all* of the things I listed earlier, we need forge federation.

## Forge federation is just a convenience problem

When people ask me about what forge federation is, I usually just tell them that it's so you can use a single account to interact with users and projects on any forge. This is easy to understand and people usually get it immediately, but it's also somewhat misleading and sells short the potential of forge federation, because it rephrases things as a convenience problem.

Whenever I see an interesting project on a self-hosted Gitea instance, as much as I admire the project, I'm not going to create an account on that instance just to give the project a star. Even if I want to report an issue, I still need to take a second to think about whether it's worth creating an account on this random instance. So yes, there is a convenience problem here which forge federation tries to solve, but our long-term goal is much bigger than this.

Forge federation is ultimately about creating an open and interoperable ecosystem for forges and all sorts of other code collaboration software. Just look at GitHub: it's the epicenter of a giant ecosystem of apps and integrations and tools that cements GitHub's position. It's a textbook example of a walled garden. We want to replicate this, but in an open ecosystem where everything speaks the ForgeFed protocol so you're never locked-in by which forge or code collaboration software that you use. We're basically an extension of the [fediverse](https://en.wikipedia.org/wiki/Fediverse), but for code collaboration!

There's a great discussion about this [here](https://forum.forgefriends.org/t/repositioning-forgefed-scope-to-code-forges-or-free-software-development-lifecycle-fsdl/713).

## Forge federation is just fancy single sign-on

A very common misconception about Gitea federation is that once Gitea gets federation support, you'll be able to go to any Gitea instance and log in, kinda like fancy single sign-on (SSO).

While that sounds like a smart way to solve the convenience problem I talked about above, it's also completely wrong.

Why? Because SSO only solves the convenience problem of creating new accounts, and nothing else. None of the grand vision above is possible if we go down the SSO route.

So how does Gitea federation actually work? The best analogy I can come up with is Mastodon. With Mastodon, you have an account on a single instance, and content from other instances is rendered on your instance. When using Mastodon, you rarely leave your own instance. You can view remote accounts and remote content because Mastodon automates the process of fetching the remote stuff and rendering it on your instance.

It's no coincidence that this is also exactly how Gitea federtion works, since both Mastodon and Gitea use the ActivityPub protocol (Gitea also uses the ForgeFed extension to ActivityPub which contains forge-related types and behaviors).

## Gitea federation is easy to implement

If you equate "easy" with "lines of code", then yes, Gitea federation is as easy as a few thousand lines of code.

But people have been working on Gitea federation for a year now. The hard, really really hard, part is rethinking the internal architecture of Gitea and how to best add federation support. Mastodon was designed from the start with federation in mind. We're adding federation support to a several-year-old, mature Git hosting software (since starting from scratch would take forever and duplicate a ton of work). For Gitea fedeation, we have to find creative ways to reuse the existing database tables and functions and make changes to deep internal code. It's not a massive amount of changes, since we are reusing so much stuff, but it's far from trivial.

Also, ActivityPub is pretty intimidating to learn until you realize it's actually not too bad.

## There are too many forge federation projects and all the developers should collaborate on a single project

A common critique of the libre software world is that there's way too much fragmentation and duplication of work. Just look at the number of Linux distros, or programming languages, or window managers...

Forge federation has a surprising number of projects too, sometimes with confusing names: ForgeFed, Forgefriends, Forgeflux, Gitea federation, Vervis, Pagure, the Friendly Forge Format, and so on. Wouldn't it be so much better if we just all worked together on getting ForgeFed and Gitea federation ready (since that's what most people care about) and forgot about everything else?

If you're noticing the general trend with these myths, you'd probably guess that this is also completely wrong. All of those projects above have different goals and aims. For instance, Gitea isn't working on a federated project search tool, but Forgeflux's Starchart and North Star projects are filling in that gap. There is some overlap between projects, but the developers collaborate and help each other's projects improve. Also, our long-term goal is to foster a whole ecosystem of interoperable code collaboration software, so enforcing a Gitea monoculture seems contrary to that goal.

