---
title: "Easily Resolving Git Merge Conflicts"
description:
date: 2023-03-13T10:00:00-06:00
slug: "merge-conflicts"
image: pexels-delcho-dichev-517884.jpg
draft: false
tags:
  - git
---

## When it rains it pours

It seems like whenever there's a merge conflict, it's rarely just one file in a single commit. It's always multiple files in multiple commits, and that's where the endless stream of pain comes when you do things like cherry pick multiple commits or run `git pull origin main`.

Merge conflicts in git are a necessary evil _because_ of the nature of git. Git has zero insight into which changes precede over eachother, and it relies on the user to make those informed decisions.

## Making things easier with merge strategy options

Any command that involves a merge conflict is also going to include the optional parameter for specifying a merge strategy. `-X ours` and `-X theirs` are two neat ways to say

> If there are conflicts, prefer these changes over those

## In Practice

If you want to try it for yourself, run this shell script!

```fish
git branch a
git branch b

git checkout a
echo "a" > foo
git add foo
git commit -m "added a to foo"

git checkout b
echo "b" > foo
git add foo
git commit -m "added b to foo"

git merge a -X theirs
```

After running this script, `foo` in branch `b` will be overwritten with the changes from branch `a`. You can safely delete both branches `a` and `b` without reprecussions (unless for some reason you have a branch that already exists named a or b).
