---
title: "Answering Forgejo Federation Questions"
date: 2023-01-10T21:47:26Z
draft: true
description: "This post answers some commonly asked questions about Forgejo federation"
type: "post"
tags: ["federation", "forgejo", "activitypub"]
---


### What is federation?

Federation is about creating open protocols so that different servers can communicate with each other. Once Forgejo gains federation support via the [ForgeFed](https://forgefed.org/) protocol, you'll be able to do things like create issues for projects on other Forgejo instances without creating an account on that instance. But federation doesn't just solve the problem of having to create many Forgejo accounts. The power of federation comes from building an interoperable ecosystem, since any other code collaboration site or development tool can also add ForgeFed support. Just look at GitHub: it's the epicenter of a giant ecosystem of apps and integrations and tools that cements GitHub's position. It's a textbook example of a walled garden. We want to create something similar, but in an open ecosystem where everything speaks the ForgeFed protocol, so you're never locked-in by which forge or code collaboration software that you use.

### Is Forgejo federation development being worked on right now?

Yes. You can find an up-to-date task list [here](https://codeberg.org/forgejo/forgejo/issues/59).

### When will federation be released?

Forgejo federation will be merged into mainline Forgejo through two (or more) pull requests. The first pull request, which will be submitted later this month, includes backend support for some basic federation features like following remote users, opening issues on remote repositories, and blocking instances. A second pull request will include a UI for federation, more moderation features, and backend support for federating all of Forgejo's features, such as adding remote users as repository collaborators. Federation will most likely be included in Forgejo 1.19, which will be released in a few months.

### Will Gitea also gain federation?

Most likely yes. We want as many forges as possible to be federated, so we will contribute the federation code upstream to Gitea.

### What's F3 and how does it relate to federation?

A lot of difficult and seemingly unrelated problems like migrating projects, federated pull requests, and mirroring issues actually have the same solution. A project's code is stored using Git and can be easily migrated, but the issues, pull requests, and project metadata are stored in the database. If only we had a common format for representing all the various components of a project and code to serve and store project components using this format... then mirroring issues would be as easy as fetching remote issues in this format and storing them on our instance. I know, [let's make our own format](https://xkcd.com/927/)!

And that's why [F3](https://forum.forgefriends.org/t/about-the-friendly-forge-format-f3/681) was created. It's a JSON format closely based on how projects are internally stored in Forgejo's database. Although it has a similar semantics, it is different from the [ForgeFed vocabulary](https://forgefed.org/vocabulary.html). Since the rest of Forgejo federation uses ForgeFed, we're working on unifying F3 and the ForgeFed vocabulary so they can use the same codebase.

### Further reading

If you think this post was helpful, you should read [Forge Federation Myths](https://a.exozy.me/posts/forge-federation-myths/). Note that it predates Forgejo, so you might want to mentally substitute `s/Gitea/Forgejo/g` while reading it.
