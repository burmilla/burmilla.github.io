---
title: Users
weight: 1
---
# Users

Currently, we don't support adding other users besides `rancher`.

You _can_ add users in the console container, but these users will only exist as long as the console container exists. It only makes sense to add users in a [persistent consoles](/docs/installation/custom-builds/custom-console#console-persistence).

If you want the console user to be able to ssh into BurmillaOS, you need to add them
to the `docker` group.
