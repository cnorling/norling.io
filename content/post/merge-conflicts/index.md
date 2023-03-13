---
title: "Easily Resolving Merge Conflicts"
description:
date: 2023-03-02T10:00:00-06:00
slug: "merge-conflicts"
image: pexels-delcho-dichev-517884.jpg
draft: true
tags:
  - git
---

## When it rains it pours

It seems like whenever there's a merge conflict, it's rarely just one file in a single commit. It's always multiple files in multiple commits, and that's where the endless stream of pain comes when you do things like cherry pick multiple commits or run `git pull origin main`.

Merge conflicts in git are a necessary evil _because_ of the nature of git. Git has zero insight into which changes precede over eachother, and it relies on the user to make those informed decisions.

## Making things easier with merge strategy options

Any command that involves a merge conflict is also going to include the optional parameter for specifying a merge strategy. `-X ours` and `-X theirs` are two neat ways to say

> If there are conflicts, prefer my changes over theirs

or

> If there are conflicts, prefer their changes over mine

## In Practice
