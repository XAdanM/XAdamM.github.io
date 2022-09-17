---
title: "The Path Out of Stagnation"
date: 2022-09-17T01:58:53Z
draft: true
description: "How I plan to get Gitea federation development moving again"
type: "post"
tags: ["gitea", "federation", "development", "life"]
---


If you've been following Gitea federation development recently, you've probably noticed there hasn't been much recently. The last real commit was 25 days ago. More recent changes have all been to pull upstream changes and fix breakage.

There's a reason behind that. And yes, it's not because I'm dead, because I'm pretty sure I'm doing just fine and alive right now.

Actually, I've just been so occupied with other life things, that once I've gone on a temporary hiatus from Gitea federation development, it's just difficult to get back.

My primary goal right now is to get [PR #20391](https://github.com/go-gitea/gitea/pull/20391) ready and merged. The PR is a great start, but yeah, it's nowhere close. There's a TODO list on that issue, but here's a more detailed list of things to do:

- Serve issues, pull requests, and comments at an API endpoint
- Finish send activity function implementions
- Finish receive activity function implementations
- Send out activities to followers when starring or creating a repo
- Make internal functions call send activity functions
- Create redirect UI for redirecting from a remote instance to your instance
- Implement issue state changes
- Use AGit flow for federated PRs to save disk space
- Write integration tests (oh yeah, testing is a thing)

And that's it! Not too bad, right? Only 20 hours of work...

So here's my plan to actually find 20 hours to write the code: do an hour each day. Every day.

I previously always worked on Gitea in short, a few-day-long productive spurts with long gaps in between. That's worked out... well not great, looking at my progress so far. Anyways, I'd better get started writing some code now.
