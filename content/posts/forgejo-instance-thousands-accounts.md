---
title: "Why Does My Forgejo Instance Have Thousands of Accounts?"
date: 2023-01-11T19:46:13Z
description: "An analysis of why Mastodon's account deletion feature caused my Forgejo instance to gain thousands of accounts, and how I dealt with it"
type: "post"
tags: ["activitypub", "federation", "forgejo", "mastodon"]
---


A few months ago, my [Gitea instance](https://git.exozy.me) for dogfooding federation was [mysteriously spammed by hundreds of accounts from banana.dog](https://social.exozy.me/@ta180m/108766017685742945). Even more strangely, the banana.dog instance itself was gone, and replaced by some weird static website. (Bad pun: maybe banana.dog didn't like my dogfooding) I wrote a [script to clean up the spam](https://social.exozy.me/@ta180m/108830406991961663), and then promptly forgot about the whole incident.

Fast forward to now, and my Gitea-turned-Forgejo instance now has 1816 users! These aren't normal users. In fact, most of them don't even exist! For instance, the lexicographically first account, 0xfr33d@mstdn.social, gives me a 410 error when I try fetching its actor with `curl -H 'Accept: application/activity+json' https://mstdn.social/@0xfr33d | jq`.

I know that my Forgejo federation code creates a new user whenever a remote user interacts with my instance, but how can these accounts interact with me if they don't even exist?

I vaguely remember hearing a month ago about performance problems due to Mastodon flooding the fediverse with user deletion activities... that's it! No idea why it took me so long to figure that out. The banana.dog debacle must have been because the instance self-destructed, so it flooded the fediverse with `Delete` activities to mass-delete all its accounts. Forgejo doesn't handle that type of activites yet, so my instance ended up creating all those accounts and not deleting them.

Adding support for `Delete` activities was surprisingly easy. First, I waited a few minutes for one to arrive and printed it out:
```json
{
	"@context": "https://www.w3.org/ns/activitystreams",
	"type": "Delete",
	"object": "https://red.niboe.info/users/plr",
	"actor": "https://red.niboe.info/users/plr",
	"audience": [],
	"tag": [],
	"to": ["https://www.w3.org/ns/activitystreams#Public"],
	"bto": [],
	"cc": [],
	"bcc": [],
}
```

Cool, so we just have to make sure to check that the `actor` and `object` match so you can't just delete someone else's account. Here's the full implementation:
```go
func delete(ctx context.Context, delete ap.Delete) error {
	actorIRI := delete.Actor.GetLink()
	objectIRI := delete.Object.GetLink()
	// Make sure actor matches the object getting deleted
	if actorIRI != objectIRI {
		return nil
	}
	
	// Object is the user getting deleted
	objectUser, err := activitypub.PersonIRIToUser(ctx, objectIRI)
	if err != nil {
		return err
	}
	return user_service.DeleteUser(ctx, objectUser, true)
}
```

Alright, that prevents any future spam, but what about cleaning up the thousands of accounts already on my instance? First, let's fetch all of them using the Forgejo API:
```bash
curl https://git.exozy.me/api/v1/users/search -o users.json
```

And then use some Python to get the deleted users:
```python
from json import load
from requests import get

l = load(open('users.json'))

for u in l['data']:
	if '@' in u['username']:
		namesplit = u['username'].split('@')
		iri = 'https://'+namesplit[1]+'/@'+namesplit[0]
		code = get(iri, headers={'Accept': 'application/activity+json'}).status_code
		if code != 200:
			print(iri, code)
```

Since my instance has so many users, the script takes a few minutes to run. Once it's finished, we can purge all of them with:
```python
from os import system

with open('users') as f:
	for line in f.readlines():
		irisplit = line.split()[0].split('/')
		name = irisplit[3][1:]+'@'+irisplit[2]
		print(name)
		system('sudo -u forgejo forgejo admin user delete --username '+name+' --work-path /var/lib/forgejo -c /etc/forgejo/app.ini')
```

Watching this script run is some seriously cool eye candy! Forgejo reports the amount of time in microseconds that each SQL operation takes, so you can watch the numbers flash by beautifully as it delete hundreds of accounts. It takes around 0.067 seconds on my server to delete a user.

Here's a full breakdown of where all the deleted accounts were from:
```bash
cat users | cut -d'/' -f1-3 | sort | uniq -c
     29 https://baraag.net
      1 https://bolha.us
      1 https://ck.cafkafk.com
     14 https://cybre.space
      1 https://det.social
      6 https://equestria.social
      1 https://estrogen.network
     11 https://gameliberty.club
      1 https://glitch.social
      4 https://gruene.social
     39 https://hachyderm.io
      3 https://helladoge.com
      3 https://helvede.net
    438 https://mastodonapp.uk
      1 https://mastodon.arch-linux.cz
      2 https://mastodon.cloud
     44 https://mastodon.sdf.org
      1 https://medibubble.org
      1 https://mk.autonomy.earth
      1 https://mk.gabe.rocks
     22 https://mstdn.io
   1042 https://mstdn.social
      1 https://ohai.social
      1 https://pl.fediverse.pl
      1 https://pouet.chapril.org
      1 https://social.lol
      1 https://social.tubul.net
     30 https://techhub.social
      1 https://test.exozy.me
      1 https://www.librepunk.club
```

That ended up deleting 93% of the users on my instance. Much cleaner now!
