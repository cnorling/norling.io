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

## So What is a Git Rebase?

A git rebase is the act of disassembling and reassembling a range of commits. The function's name comes from its original purpose of changing the forked location of your branch to a different commit or branch.

## How do I Git Rebase?


### Determine What Commits to Rebase

You can use most valid forms of [gitrevision syntax](https://git-scm.com/docs/gitrevisions), but `git log --oneline` and `git reflog` are the most common options for discovering historical edits. `git log --oneline` will only show you commits. `git reflog` will show you commits in addition to actions you take like checking out branches, other rebases you've done, git resets, commit amends, and other historical modifications made to git. The only time I use referential syntax is if I'm recovering a previously deleted commit since git will locally log the commit even after deleting it.

![an example showcasing the differences between a git log and git reflog with git commits in their relative positions](reflog-vs-log.png)

Here's a table of a few valid gitrevision syntaxes you can use in a rebase, and what they roughly translate to.

|syntax|meaning|
|------|-------|
|`016a428`|rebase all commits between HEAD and `016a428`|
|`HEAD@{5}`|rebase all commits contained in my last 5 git actions|
|`HEAD~5`|rebase the last 5 commits|
|`HEAD@{5hr}`|rebase all commits made in the last 5 hours|
|`':/^Initial'`|rebase all commits made between HEAD and the first commit found with a commit message that matches the regular expression `^Initial`|

![an example of the output from git log --oneline](git-log-output.png)

### Edit Your git-rebase-todo

A `git-rebase-todo` file is a manifest containing all the commits in your rebase that you can edit to instruct git on what changes you want to make. It includes the action you want to do, the commit hash, and the commit message for easy identification. An example looks like this:

```fish
pick e9f16f3 baz
pick ff783de buz

# Rebase aa7b423..ff783de onto aa7b423 (2 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified); use -c <commit> to reword the commit message
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

The only commands in that list I haven't used are exec, break, label, reset, and merge.

### Start The Rebase

As soon as you save the `git-rebase-todo` file and close it, the rebase will start. The first step git does is it saves all your commits into a staging area for safekeeping if you need to perform any reversions. Then it goes through each commit oldest to newest. Actions like pick, squash, fixup, and drop will be executed automatically. Actions like reword and edit will pause for you to make your requested changes. If it's a git message change, it will open the commit message file and allow you to make your changes.

Edit steps are a bit more complex. Git has no idea what modifications you intend to make on the edit step, so you need to tell it when you're finished. You can make any changes you need to the commit, and once you're finished you run `git commit --amend` to add your content to the commit. Then run `git rebase --continue` to tell git you're done with the current commit.

## Deleting Commits

## Editing Commit Content

## Combining Commits by Squashing

## Changing the Base of Your Branch

An example of that might be if you created branch `topic` while checked into branch `next` instead of branch `master` and you've already committed content you don't want to lose. `git rebase --help` has some nicely defined diagrams of what this would look like as illustrated below, or [here](https://git-scm.com/docs/git-rebase) if you want to read the official docs.

## Signing Older Commits

The easiest way to sign your older commits is to setup your git config to automatically sign commits, then execute a rebase on all your commits. If this sounds like too much, it might be best to just leave your commits unsigned. Github has excellent documentation on setting up commit signatures [here](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits) and how to automatically sign commits [here](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key).
