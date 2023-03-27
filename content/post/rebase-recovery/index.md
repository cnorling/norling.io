---
title: "Recovering From a Botched Git Rebase"
description:
date: 2023-03-13T10:00:00-06:00
slug: "rebase-recovery"
image: pexels-delcho-dichev-517884.jpg
draft: true
tags:
  - git
---

![we've all been there](rebase-messup.jpg)

When rebasing, it's inevitable that you will inaverdently delete something you didn't intend to, or finish a rebase before you were actually done. When this happens, the best thing you can do is take a step back and truly evaluate the current state of the remote repository and your local repository.

- Is the content actually deleted?
- Have I pushed my changes out yet?
- Could someone else have a copy of the content I deleted?

Even if you've already force pushed out your changes, there are ways to recover deleted content.

## Meter your Expectations

Git rebasing is completely disassembling and reassembling commits. If your rebase includes more than just a few commits, it's likely you won't be able to produce the

## Check if There's Anything in Git Reflog

Git Reflog is a _referential log_ of all the states your local git repository has been in. Every removed branch, deleted commit, unsigned commit, previously squashed commits, even viewing a specific commit SHA is logged and recorded. Reflog is a local only thing, so if you delete a local git repository and clone it down again you'll lose all your reflogs.

You can access your referential logs by running `git reflog`. By default, it retains the last 90 days of git activity. Every time you run a git specific action, git runs a garbage collector that discards anything outside the defined retention period. If you wanted to, you could increase this threshold by running `git config gc.reflogExpire 120`. Don't forget to pass `--global` if you want it to apply to all repositories.

### Accessing Reflogs

All reflogs are dynamic and measured according to an offset from your latest action. To access reflogs, first you need to identify which reflog to examine. Run `git reflog` to identify the state you want to examine.

## Example
