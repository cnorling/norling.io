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

When rebasing, it's inevitable that you will delete something you didn't intend to, or finish a rebase before you were actually done. When this happens, the best thing you can do is take a step back and truly evaluate the current state of the remote repository and your local repository.

- Is the content actually deleted?
- Have I pushed my changes out yet?
- Could someone else have a copy of the content I deleted?

Even if you've already force pushed out your changes, there are ways to recover deleted content.

## Temper your Expectations

When you git rebase, you're completely disassembling and reassembling commits. If your rebase includes more than just a few commits, it's likely you won't be able to reach parity between the old and new commit history. It's possible, but generally not worth the effort of plucking each commit and regenerating the repository's state 1:1. The best you should aim for is having the `HEAD` of your branch hydrated with the correct content.

## Check if There's Anything in Git Reflog

Git Reflog is a _referential log_ of all the states your local git repository has been in. Every removed branch, deleted commit, unsigned commit, previously squashed commits, even viewing a specific commit SHA is logged and recorded. Reflog is a local only thing, so if you delete a local git repository and clone it down again you'll lose all your reflogs.

You can access your referential logs by running `git reflog`. By default, it retains the last 90 days of git activity. Every time you run a git specific action, git runs a garbage collector that discards anything outside the defined retention period. If you wanted to, you could increase this threshold by running `git config gc.reflogExpire 120`. Don't forget to pass `--global` if you want it to apply to all repositories.

### Accessing Reflogs

All reflogs are dynamic and measured according to an offset from your latest action. To access reflogs, first you need to identify which reflog to examine. Run `git reflog` to identify the state you want to examine. Then run `git checkout HEAD{23}` where 23 is the number offset from `git reflog`.

In this state, you can poke around and look at the file content to confirm that it is what it should be.

### Accessing Git Logs Within Reflogs

Since reflogs are a capture of the repository's state at that time, they also have the historical output from `git log`. If you need to identify a commit SHA that's no longer in `git log`, you can checkout an earlier reflog and run `git log` to browse commits that were associated with your branch at that point in time. It's very useful for identifying deleted content.

![Even reflogs have logs](reflog-log.png)

## Do You Have the Commit SHA?

The hard part is knowing and discovering the commit SHA you intend to recover. Even after you delete commits, they still exist both locally and (maybe) remotely. If you're trying to recover a deleted commit and you know the commit SHA, you can run `git cherry-pick $SHA` to get that commit back. After the cherrypick, the commit SHA might change since it's now incorporating the changes between how far back from head it was and where it is now.

_(unless the deleted commit was the latest one, then it will be the same)_

![Commits never die, they just fade away](soft-delete.png)

## Examples

The easiest way to learn is by doing. The following sections will have codeblocks you can run in a git repository to test what rebase recoveries are like. I'd highly recommend testing before you're working in a repo for real.

### Cherrypicking Deleted Commits

### Removing Garbage Duplicate Commits

If you're new to git rebases, it's easy to be led down the wrong path and accidentally `git pull` before you `git push --force-with-lease`. In this scenario, we've

### Un-Squashing

Okay this one's tough. I'm going to reiterate that it's better to just get a working state and move on, but if you _really_ want to try and recreate the state pre-squash you're more than welcome to give it a try. If you're going to attempt this, you'll definitely want the output of `git log --oneline` from before you squashed. If you don't have that, don't even try.

# Summary

Unless you've configured your git garbage collector to discard
