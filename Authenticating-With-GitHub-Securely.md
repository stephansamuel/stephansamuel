# Authenticating With GitHub

I've been using GitHub for a while. We used to be able to just push and pull
with `git` freely. No pesky user names or password, all that stuff.

Then GitHub decides to go all secure and stuff. Poo.

Okay, so it's not their fault. In fact, they're doing the right thing, even if
it seems annoying. It seems there are lots of bad actors doing bad stuff using
GitHub these days.

Now I have to do some super-secret key magic.

If you work for an organization where IT gives you laptops and devops gives you
keys and configurations, great. I've held both roles and these people are truly
magicians. It's not easy getting stuff to _just work_ every time.

I have some personal repos too though, including this one. That leaves me to
play magician on my own time. Once again, poo.

If you just want to get to the steps, go to [the tl;dr](#steps-to-follow-tldr).

- [Authenticating With GitHub](#authenticating-with-github)
  - [What We're Trying To Do](#what-were-trying-to-do)
  - [Prerequisites](#prerequisites)
  - [Steps To Follow (tl;dr)](#steps-to-follow-tldr)

## What We're Trying To Do

My desktop runs Linux. I use VSCode. I try to keep all my dev stuff in Docker
containers to eliminate managing things like configurations and versioning for
all the different platforms (it's a long, long list). I want to be able to use
all the VSCode `git` sugar and be able to run CLI `git`. I don't want to be
harassed for a key every time I do anything. I also don't want to leave all the
keys and stuff in plaintext.

I use a desktop and I'm not worried about physical security, but still.
Realistically, I could just write a bunch of passwords on sticky notes and my
setup would be at least as secure as most organizations. At least, until
someone asks me to bring some code somewhere and one of my laptops has to now
travel with a sticky note collection.

This whole thing has to be repeatable. Most of my hardware runs Linux but it
would be nice to be able to do everything on Windows too.

## Prerequisites

1. GitHub has some good
   [general information](https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls)
   with links to useful stuff. I used SSL for a long time but it seems that we
   are urged to use HTTPS. We're going to follow the **HTTPS URL** directions.
1. One of the base requirements is having a working GitHub account. I'm not
   using any paid account or anything, but everything should be set up
   including **email verification.** This is outside the current scope because
   if you're here, you're probably already good.
1. Some stuff needs to be installed. For starters, **`git`.** My Ubuntu
   22.something installation came with **`seahorse`** but you should be able to
   pull it from the default sources with `apt`, `dnf`, etc.
   [This article](https://kasunc.medium.com/git-credential-management-with-gnome-passwords-and-keys-seahorse-in-linux-e7b59b3b4d3d)
   says you should have `git-credential-libsecret` too but I haven't been asked
   for anything like that yet.
1. Obviously, have **a repo** ready to use. I find that it's easier for me to
   create them in the GitHub web UI then `clone` them locally. Once again, if
   you're here, you probably already have your own workflow.
1. While we're at it and since this is about security, set up **MFA** on your
   GitHub account. It's rather painless and it doesn't seem to nag to
   reauthenticate very often.
1. You also need a password manager, or a password management system. Use
   sticky notes as your own risk, or better yet, devise a real system. You're
   a developer so you shouldn't be afraid of technology.

## Steps To Follow (tl;dr)

1. Install the [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager/blob/release/docs/install.md#debian-package).
   It's a download-and-`dpkg`, not `apt` so you have to get the [.deb file](https://github.com/GitCredentialManager/git-credential-manager/releases/latest)
   and do the things. I didn't have any package conflicts and I hope you don't
   either.
1. Running `git-credential-manager configure` is disconcerting. It doesn't
   really look like it does anything. That's normal.
1. Set up `git` with the [`secretservice` credential store](https://github.com/git-ecosystem/git-credential-manager/blob/release/docs/credstores.md#freedesktoporg-secret-service-api).
   Once again, slightly disconcerting. (I used the `git config` route. If you
   use `export`, you really shouldn't be expecting anything.)

   At this point, we're set up to do authentication through the standard
   library and `seahorse`. Now we have to generate keys.
1. [Create an access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).
   You can pick either fine-grained or classic.

   For my application, I chose classic because the fine-grained ones seem to be
   better-suited for organizations with accounts and stuff. The link helps you
   decide with a few bullet points.

   When assigning permissions, especially with a classic, don't overdo it. In
   the first PAT step of [this SO post](https://stackoverflow.com/a/67360592/5583468)
   it says, "`repo`, `repo_hook` maybe," which is probably the best advice.
   What you're assigning here is just what you can do _with this token,_ not
   in general. If you're like me and you do most of your work through the web
   UI, don't give anything to your token other than the `repo` branch.

   Bear in mind that you can add permissions to a token later. Permissions can
   be updated freely and easily from the same place where you created the
   token. What you _cannot_ do is unwind unauthorized changes made if your
   token gets compromised later because you gave out too many permissions.
1. **Save the token it gives you.** That's basically an auto-generated
   password. You'll need it at least once for every machine where you log in.
   Keep it in your password manager.

   `seahorse` will keep it for you and you'll be able to copy it later if you
   don't want to use another system. That's local though, so if you lose the
   contents of the machine you're on, it goes away. This may not be so bad
   because you can always regenerate it later.

1. Go to your repo and do your `clone`, `push`, or whatever needs the
   GitHub auth. For the user name, enter your GitHub user name. For the
   password, enter the token.

   If you've done everything correctly (hopefully), `seahorse` should now show
   another Login (under Passwords) with your info.

At this point, Linux should be automatically notified by `git` when there's an
auth challenge and serve your token.
