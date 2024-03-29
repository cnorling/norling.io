---
title: "Rewriting History With Git Rebases"
description:
date: 2023-03-06T10:00:00-06:00
slug: "git-rebase"
image: stars-ijf3k5368.jpg
draft: false
tags:
  - git
---

Have you or a loved one ever wanted to:

- delete a commit
- edit the content of a commit
- remove a specific file that was accidentally added to a commit
- include missed files in a commit
- fix a typo in an old commit message
- combine a bunch of commits together

If you've worked with git, you've likely needed to do at least one of these at some point. And if you're like me, you've probably read over the `--help` documentation of git to figure out how to do some of these things, and oh boy the docs can be cagey about telling you what exactly to do. This article is a reference on just a few things you can do with git rebases, and how to do it. [Here's the equivalent article](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History) directly from the git documentation if you're interested in further reading.

## Rewriting history is controversial

_Rebasing involves rewriting history._ \
_Rewriting history involves force pushing to overwrite the remote state of a repository._ \
_Force pushing has the potential to delete others work (if you rebase recklessly)._

Rewriting history is controversial. Some say it's okay to modify history because it's sometimes necessary and can enhance git history. Others say it's not okay because git is supposed to be a historical representation of all changes, and you should prefer to roll your fixes forward. In my opinion if your historical modifications don't impact others, it should be acceptable to rewrite history. It's important when managing git history to ensure that you aren't impacting other people's work. You should follow these general practices:

- Always use `--force-with-lease` when pushing out rebases.
- Avoid rewriting history if you're collaborating with others on the same branch.
- If you have to rebase changes while working with others, `git pull` often to always make sure you're working with the latest content.
- Don't edit other people's commits unless they know about and consent to the edits.

## So what is a git rebase?

A git rebase is the act of disassembling and reassembling a range of commits. The function's name comes from its original purpose of changing the forked location of your branch to a different commit or branch. There are two general types of rebases. Ones that require human interaction to complete, and ones that do not. A human interactive rebase would involve doing things like adding, editing, deleting, combining, and splitting commits. A noninteractive rebase would be attaching your base to a different branch.

Interactive rebases are executed by running `git rebase --interactive $GITREVISION`
Non-interactive rebases are executed by running `git rebase $GITREVISION`

## How do I git rebase?

### Determine what commits to rebase

You can use most valid forms of [gitrevision syntax](https://git-scm.com/docs/gitrevisions), but `git log --oneline` and `git reflog` are the most common options for discovering historical edits. `git log --oneline` will only show you commits. `git reflog` will show you commits in addition to actions you take like checking out branches, other rebases you've done, git resets, commit amends, and other historical modifications made to git. The only time I use referential syntax is if I'm recovering a previously deleted commit since git will locally log the commit even after deleting it.

![an example showcasing the differences between a git log and git reflog with git commits in their relative positions](reflog-vs-log.png)

Here's a table of some gitrevision syntaxes you can use in a rebase, and what they roughly translate to.

| syntax       | meaning                                                                                                                          |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| `016a428`    | rebase all commits between HEAD and `016a428`                                                                                    |
| `HEAD@{5}`   | rebase all commits contained in my last 5 git actions                                                                            |
| `HEAD~5`     | rebase the last 5 commits                                                                                                        |
| `HEAD@{5hr}` | rebase all commits made in the last 5 hours                                                                                      |
| `':/^Foo'`   | rebase all commits made between HEAD and the first commit found with a commit message that matches the regular expression `^Foo` |

![an example of the output from git log --oneline](git-log-output.png)

### Edit your git-rebase-todo

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

### Start the rebase

As soon as you save the `git-rebase-todo` file and close it, the rebase will start. The first step git does is it saves all your commits into a staging area for safekeeping if you need to perform any reversions. Then it goes through each commit oldest to newest. Actions like pick, squash, fixup, and drop will be executed automatically. Actions like reword and edit will pause for you to make your requested changes. If it's a git message change, it will open the commit message file and allow you to make your changes.

Edit steps are a bit more complex. Git has no idea what modifications you intend to make on the edit step, so you need to tell it when you're finished. You can make any changes you need to the commit, and once you're finished you run `git commit --amend` to add your content to the commit. Then run `git rebase --continue` to tell git you're done with the current commit.

### Push up your rebase

If you're editing any commits that aren't strictly local to your machine, you'll have to instruct your remote git website that yes, you did mean to delete that commit. To do this, run `git push --force-with-lease`. If you don't, git will reject your changes when you try and push because it thinks it has content you're missing that you need to pull down.

`--force-with-lease` is the same as `--force` except it has a failsafe to abort if there are remote changes your local git client doesn't know about. Always use it when force pushing changes.

## Deleting commits

1. Determine what commits you want to delete by running `git log --oneline` and find the hash of the commit you intend to delete

```fish
486f3bf (HEAD -> main) e18155c658
28cabde 0523d2af6c
58098cf 5a0be3f060
add66be 8759405589 # let's delete this commit
76d462d f6ecfd8500
beeef8f (origin/main, origin/HEAD) Initial commit
```

2. Go one past that commit and copy its hash
3. Run `git rebase --interactive 76d462d`
4. Modify the `git-rebase-todo` to delete the commit

```fish
drop add66be 8759405589 # I've change the action on this commit to drop
pick 58098cf 5a0be3f060
pick 28cabde 0523d2af6c
pick 486f3bf e18155c658
```

5. Save and close the `git-rebase-todo`
6. Run `git push --force-with-lease` if working with remote changes to overwrite history

If everything worked, the commit should be gone. You can run `git log --oneline` to validate its deletion.

```fish
6144ecd (HEAD -> main) e18155c658
28971d0 0523d2af6c
ee09104 5a0be3f060
# yay the commit is gone!
76d462d f6ecfd8500
beeef8f (origin/main, origin/HEAD) Initial commit
```

## Editing commit content

1. Determine what commit you want to edit by running `git log --oneline` and find the SHA of the commit you intend to modify

```fish
2f512ca (HEAD -> main) b8e34da2fb
1daec94 53a8322ae0
49338d3 1890202347 # let's edit this commit
6dd4f4e 3fc41f2708
10b8051 79d8d9fc4b
beeef8f Initial commit
```

2. Go one past that commit and copy its commit SHA
3. Run `git rebase --interactive 6dd4f4e`
4. Modify the `git-rebase-todo` to include your request to edit the commit

```fish
edit 49338d3 1890202347 # I've changed the action on this commit to edit
pick 1daec94 53a8322ae0
pick 2f512ca b8e34da2fb
```

5. Save and close the `git-rebase-todo`
6. Git will pause on the commit you want to edit
7. Make your changes and run `git commit --amend` to include the edits
8. Run `git rebase --continue` to tell git you're done making your changes
9. Run `git push --force-with-lease` if working with remote changes to overwrite history

```fish
2093981 (HEAD -> main) b8e34da2fb
d3ecdd3 53a8322ae0
12d9704 1890202347 # this commit has a new SHA meaning the changes went through!
6dd4f4e 3fc41f2708
10b8051 79d8d9fc4b
beeef8f Initial commit
```

If everything worked, the commit should be edited with a new SHA. You can run `git log --oneline` to validate its modification.

## Combining commits by squashing

1. Determine what commits you want to squash by running `git log --oneline` and find the hash of the oldest commit you want to squash

```fish
c3640e6 (HEAD -> main) 7ce9769337
9f2e595 3c8c13dfff # let's squash these two commits
e1fce22 600affbb4a # let's squash these two commits
ed85848 1186105185 # into this commit
64378f8 c8f8d0faf9
beeef8f Initial commit
```

2. go one past that commit and copy its hash
3. run `git rebase --interactive 64378f8`
4. modify the `git-rebase-todo` to include your squashes

```fish
pick ed85848 1186105185 # this is the commit we're squashing into
squash e1fce22 600affbb4a # these are the commits we're squashing
squash 9f2e595 3c8c13dfff # these are the commits we're squashing
pick c3640e6 7ce9769337
```

5. save and close the `git-rebase-todo`
6. git will pause on the newly squashed commit and give you an opportunity to modify the commit message if desired
7. save and close the `COMMIT_EDITMSG` file to finish the rebase
8. run `git push --force-with-lease` if working with remote changes to overwrite history

```fish
8188e83 (HEAD -> main) 7ce9769337
1b4f942 1186105185 # the commit has a new SHA meaning the commits were squashed in!
64378f8 c8f8d0faf9
beeef8f Initial commit
```

If everything worked, the commit should be edited with a new SHA. You can run `git log --oneline` to validate its modification.

If you enjoyed this article, have any questions, noticed something inaccurate, or you just want to say hi feel free to drop a comment below or send an email to me@norling.io
