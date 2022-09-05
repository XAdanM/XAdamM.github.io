---
title: "Where are All the Forge Federation Developers?"
date: 2022-07-10T14:25:06-05:00
description: "If forge federation is so crucial to the future of libre software, why are there so few forge federation developers?"
type: "post"
tags: ["gitea", "federation", "forgefed", "activitypub", "development"]
---


There's a [famous story](https://en.wikipedia.org/wiki/Fermi_paradox) that during a conversation about flying saucers in 1950, physicist Enrico Fermi blurted out, "But where is everybody?"

This post isn't about extraterrestrials, but rather forge federation developers. (Fortunately, we do have convincing evidence that forge federation developers do actually exist in the universe) The SFC's recent [#GiveUpGitHub](https://sfconservancy.org/GiveUpGitHub/) campaign has shined the spotlight on an long-standing problem in the libre software world. So much free, open-source, libre code is hosted on a hostile profit-oriented proprietary platform, and we need solutions now.

The solution is not to scatter everyone across hundreds of disparate GitLab and Gitea instances or force everyone to use SourceHut's spartan mailing lists. The solution is to make forges talk to each other using a common, open protocol: forge federation. The dream is simple: you can use a single account for interacting with repos and users in the entire federation, creating a network and community of collaboration. As a [currently open PR](https://codeberg.org/ForgeFed/ForgeFed/pulls/146) on the ForgeFed repo states (I should really stop procrastinating about reviewing that PR):

> "It puts the power back into our hands to create tools and collaborate in ways that are aligned with human needs, powerful and safe ways that allow us to include everyone and that don't depend on some big company's policies or some website suddenly shutting down. Let's create the future together!"

So given the urgency and seriousness of the problem, why are fewer people working daily on forge federation than the number of fingers on my hand?

## The learning curve is terrifying

To put it bluntly, the learning curve looks like it's the size of the Himalayas. There's the ActivityPub spec that [sounds like mumbo-jumbo](https://social.exozy.me/@ta180m/108601991859976777) the first time you read it. (But I promise, it makes perfect sense after you read it enough times.) There's ForgeFed which adds a whole bunch of new types and behavior on top of ActivityPub. It's even difficult for newcomers to keep track of all the similarly named projects like ForgeFed, ForgeFlux (formerly forgefed.io for more confusion), ForgeFriends, the ForgeFed implementation in Gitea, and the Friendly Forge Format.

However, not all hope is lost! If you just want to help out a bit with, say, the Gitea federation UI, you don't need to understand much ActivityPub. Plenty of the tasks on the [Gitea task list](https://gitea.com/xy/gitea/issues/3) are actually quite approachable! There's also the [useful links](https://gitea.com/xy/gitea/wiki/Useful-links) page which is frequently updated and might be helpful when getting started.

## Everyone thought ForgeFed was dead for a long time

Up until last month, ForgeFed was near-silent at its old home at NotABug.org. Although ForgeFed initially had a very active community, during 2020 and 2021, the project had clearly stalled. It's hard to say for sure, but I think this had an enormous negative effect on forge federation as a whole and stopped the momentum of other forge federation projects.

Thankfully, all of this has been resolved and ForgeFed is back rolling again on [Codeberg](https://codeberg.org/ForgeFed/ForgeFed)!

## The road ahead

If I've now piqued your interest in contributing to forge federation, what does the road ahead look like? I'm going to focus specifically on Gitea federation here since that's my area of expertise, but there are plenty of other forge federation projects to contribute to. The following list is basically a summary of the items on the [task list](https://gitea.com/xy/gitea/issues/3).

### Starring

The goal here is to star a repository on a Gitea instance other than yours. Although much of the backend code for this has already been written, the frontend for federation features is nonexistent. Although we could get away with not having a frontend when implementing federated following by reusing the Mastodon UI, the lack of a frontend is starting to make testing and debugging a huge PITA.

So yeah, we'd appreciate all the frontend and UI help we can get!

### Collaborators

Again, the goal here is simple. Add a user from another instance as a collaborator to your repository so they can modify it. The implementation is anything but simple however, since there will now be two copies of the repo that must be kept in sync: the original copy on your instance and a copy on the remote collaborator's instance. To prevent a massive headache, we will be using the original copy as the single source of truth and the other copy is synchronized unidirectionally from the original. Another challenge is doing a cross-instance Git push, since any pushes by the remote collaborator should be redirected to the original instance.

### Forking

Alright, now we're getting into some really interesting stuff. Federated forking is actually extremely easy, if we already have federated collaborators, since the maintainers of the original repo are implicitly collaborators on the fork.

### Issues

There's not too much to say about federated issues, since it's pretty similar to microblogging (at least from a technical standpoint), and ActivityPub is perfect for microblogging.

### Pull requests

This one is some really fun stuff. The ForgeFed protocol hasn't standardized patches and pull requests yet, so we'll also have to work on that. And we also need both federated forking and issues to be done for this. This is probably the toughest item on the list.

### Notifications

I haven't really worked on federated notifications yet, but I think someone with more experience with the Gitea codebase should have no trouble with implementing this.

### Referencing remote issues in comments

This isn't in the ForgeFed protocol yet either, so I have no idea how this will actually implemented in practice.

### Migrations

Ideally, we want a way to easily migrate a Gitea account between instances, or migrate a GitHub account to a Gitea instance in one click. This is also [not yet in the ForgeFed protocol](https://codeberg.org/ForgeFed/ForgeFed/issues/149), and there are some other technical details to worry about, such as adding a cooldown period where an old username can't be claimed and periodically fetching and updating cached remote user data.

### Moderation

Moderation will be extremely important once Gitea gains federation capabilities, so we'll need a nice UI for blocking users or servers and other moderation features.

### Other

There are some other longshot or very minor tasks that I didn't mention, such as C2S ActivityPub, federated project boards, Mastodon interoperability, collection pagination, federated wikis (since it's exactly the same as federating repo code), and AP addressing. Of course, this list doesn't mention any of the other forge federation projects like ForgeFlux or ForgeFriends at all, so you can also check those out if you're interested in contributing.
