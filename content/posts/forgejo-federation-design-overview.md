---
title: "Forgejo Federation Design Overview"
date: 2023-01-09T21:19:27Z
draft: true
description: "A deep dive into the design of Forgejo federation"
type: "post"
tags: ["forgejo", "federation", "activitypub"]
---


This post describes the details of the design of [Forgejo federation](https://codeberg.org/forgejo/forgejo/issues/59).


## Identity

Forgejo, like most ActivityPub implementations, uses WebFinger to resolve @user@instance.com usernames to IRIs.

Likewise, Forgejo signs all activities using HTTP signatures

An interesting application of this is private repository federation.


## Common data format

A lot of seemingly difficult problems like federated pull requests, mirroring entire projects including issues, and migrations are


F3, ForgeFed vocabulary

Mirroring

Migrations


## Consistency

Write about primary-secondary replication for federated collaborators

Performance/scalability

Redirect Git pushes


## Moderation

Blocking users and instances

Rate-limiting and spam


## UI

Better AP UI? Fetch data from original instance instead of your instance? Performance problems?

