---
title: "Rewriting History With Git Rebases"
description:
date: 2023-03-02T10:00:00-06:00
slug: "git-rebase"
image: stars-ijf3k5368.jpg
draft: true
tags:
  - git
---

Have you or a loved one ever wanted to:

- delete a commit
- edit the content of a commit
- include missed files in a commit
- fix a typo in an old commit message
- combine a bunch of commits together
- sign older commits
- change what branch you've forked from

If you've worked with git, you've likely needed to do at least one of these at some point. And if you're like me, you've probably tried to read the `--help` documentation of git to figure out how to do some of these things, and oh boy the docs can be hard to read. This article is a reference on what you can do with git rebases, and how to do it.

# So What is a Git Rebase?

A git rebase is the act of disassembling and reassembling a range of commits. The function's name comes from its original purpose of changing the forked location of your branch to a different commit or branch. An example of that might be if you created branch `topic` while checked into branch `next` instead of branch `master` and you've already committed content you don't want to lose. `git rebase --help` has some nicely defined diagrams of what this would look like as illustrated below, or [here](https://git-scm.com/docs/git-rebase) if you want to read the official docs.

![an example of what happens when you run git rebase --onto master next topic](rebase-transplant.png)

## Determine What Commits to Rebase

You can target a range of commits using [gitrevision syntax](https://git-scm.com/docs/gitrevisions). The most common method I use to determine what range of commits to target is by running `git log --oneline` like so:

The only time I use referential syntax is if I'm recovering a previously deleted commit since git will locally log the commit even after deleting it.

## Deleting Commits

## Editing Commit Content

## Combining Commits by Squashing

## Changing the Base of Your Branch

## Signing Older Commits