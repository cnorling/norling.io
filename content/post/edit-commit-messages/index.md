---
title: "Editing Git Commit Messages"
description:
date: 2023-02-25T10:00:00-06:00
slug: "edit-commit-messages"
image: stars-gdb805d2e7_1920.jpg
draft: true
tags:
  - git
---

You can edit your most recent commit message by running `git commit --amend`

If you've already pushed out your commit, you'll have to tell the remote git repository that you intend to rewrite history. You can do this by running `git push --force-with-lease`

But what about older commits? 

You can edit older commits with an interactive rebase. The first step of a rebase is identifying the rebase's span. Run `git log --oneline` to get a top-down list of your latest commits, and select one farther than the latest commit. In the below image

![Example Image](bruh.jpg)
