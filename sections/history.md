# Rewriting history

We have already seen a few ways of undoing changes locally in the first section. We saw how the `restore` command can be
used to undo or unstage changes before commit. We also saw how to modify the last commit with `commit --amend`. Those
are not really rewriting history because the work has not been published yet, you just worked on something and tweak it 
before pushing to a remote.

We will now see a couple of commands to modify the history.

## Reverting a previous commit

The `revert` command allows you to undo the work done in a previous commit. It does not go back in time and change 
things. Running the command will create a new commit that exactly reverts the commit specified as argument. It will 
generate a simple commit message stating what is being reverted (from the commit message) and give you the option to 
rewrite the message. Reverting a commit might lead to conflicts, as subsequent commits might have modified the same
lines.

The following listing shows how to use the revert command:

```shell
git revert <hash>   # Revert given commit
```

## Altering the timeline

In the first section we saw how to use rebase when pulling from a remote. In the *branching* section we used `rebase` to
re-apply commits from one branch on top of another branch. This is an extremely powerful tool in itself. But `rebase`
can actually do *a lot* more than that! 

The *interactive* mode of `rebase` lets you alter the current branch, giving you options such as rewording commit 
messages, combine commits together or completely delete commits from the history. For that reason, it is quite 
important to be in a steady state before starting a rebase. It does not harm to create a temporary branch to *save 
your work* just in case. If you are working with a remote branch (a short-lived prototype branch for instance) do 
not forget to push the branch first.

Before we dive into the details, let us remember that toying with commits order or deleting can lead to conflicts. 
As usual, do not be afraid to abort the process and start from scratch if you have a doubt.

### What it looks like

The following listing shows how to use the interactive option of `rebase`:

```shell
git rebase -i           # Rebase current branch up to last push to the remote
git rebase -i <branch>  # Rebase current branch on to of the given branch
```

Running such a command will open the default text editor and show you something similar to the following text:

```text
pick 7df483f Section on tags
pick b41fe20 Section on creating/deleting branches
pick de23a95 Update listing

# Rebase 870d576..de23a95 onto 870d576 (3 commands)
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
```

The first section shows which commits are involved and what command will be used for those commits (in this case, 
three commits from the `create-branches` branch when rebasing it onto `main`). By default, the `pick` command is used,
meaning the commit will be taken as is. The second part (all the lines behind `#`) is only there as documentation. There
is a short description of the rebase, then it tells you the different options that are available for each commit.

If you save and exit the file at this point, the rebase will be performed and pick all the commits. If you are rebasing
a local branch onto its remote, this is a non operation, it will simply leave the branch in its current state. The same
will apply if you delete all lines with a command and a commit. If you are rebasing a branch onto another, this will 
simply take the current branch a re-apply each commit onto the other branch.

This example does not do anything more than what we have already seen in previous sections. Using the interactive 
option simply lets you see what you are doing. We will now look into how we can leverage the rebase options to alter 
the history
