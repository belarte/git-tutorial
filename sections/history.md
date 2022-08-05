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

### Simple operations

We will first see a few commands that are quite straight forward.

The `reword` command lets you change the commit message. When the rebase is performing, it will stop at the given
commit, open the default text editor and wait for you to save and exit. This might be useful if you noticed a typo
or want to add more details to the message.

The `exec` command lets you run any system command. This is for example useful when doing a complex rebase (lots of
commits merged or reordered) to verify the rebase does not introduce faulty states. The `exec` command can be added
at any time in the rebase, in between commits. The command to run can also be specified in the command line before
starting the rebase (in interactive mode or not), as shown in the following listing:

```shell
git rebase -i main --exec "make test"   # Rebase current branch on top of main, entering interactive mode.
                                        # The "make test" command will be run between each commit and once at the end.
git rebase main --exec "make test"      # Same without entering interactive mode.
```

This will produce the following in interactive mode:

```text
pick 7df483f Section on tags
exec make test
pick b41fe20 Section on creating/deleting branches
exec make test
pick de23a95 Update listing
exec make test
```

Here `make test` is just a random command, this would assume you have a makefile with a test target. It is only here
to illustrate the command. You can see the commits are the same as the previous example, the default command is `pick`.

The interactive rebase gives you the option to reorder the commits in the list. This is simply done by reordering
the commits in the file before saving and exiting. This will indeed change the history, so it can lead to conflicts.
The commits are listed in chronological order, so first one is at the top. You can also completely delete a commit.
This is either done with the `drop` command or by deleting the line. Again this can lead to conflicts.

### Modifying commits

So far we have seen how to reorganise commits without touching the content of the commits themselves. In this section
we will see how to alter existing commits.

You can combine two subsequent commits with the `squash` command. This will merge said commit with the previous in
the history. When performing the rebase, it will stop to prompt you for a new commit message. This cannot lead to
conflicts, as the two or more commits to be squashed are following one another.

The `fixup` command works in a similar fashion, but will not prompt you for a new message, it will keep the message of
the first commit. This command can also be used from the command line directly. When doing a commit, you can specify
the `--fixup` option and specify the hash of the commit to amend. This will create a special commit with all the
necessary information to modify the aforementioned commit, but without amending said commit as it could be further
away in
history (hence amending it could lead to conflict with later commits). To actually perform the fix-up, you need to
rebase and pass the `--autosquash` option. This is illustrated in the following listing:

```shell
git commit --fixup <hash>   # Create a commit that will amend the given commit
git rebase --autosquash     # When performing the rebase, the commits are actually merged
```

Last but not the least is the `edit` command. When editing a commit, the rebase will stop at said commit and leave
you to modify the code as you please. This could be a simple fix-up, but you could also do more complex things such
as resetting the commit and split it into several smaller commits. Once the modifications are done, type `git rebase
--continue` to pick up where the rebase stopped. As usual, as you are modifying the history this can lead to conflicts.

## What are the risks?

Using rebase to alter the history might be a tricky operation in itself, but once the rebase is done does not mean
that your troubles are over.

If you just rebased a local branch before pushing to a remote, you are fine. But if you rebased a tracked branch on top
of another you will have issues. As you have changed the history, it is not safe to push the modifications to a remote.
In fact, Git will prevent you to do so, as it will not be able to make sense of the different histories.

You can circumvent that by forcing the push with the `--force` option, but that is extremely risky as it might
override some remote modifications that you might not have pulled in. As a matter of fact, force push is most likely
disabled on the main branch. A safer option is to use `--force-with-lease`, it will check for remote modifications
first before forcing the push. It is safer, but it is still not foolproof.

As a general rule of thumb, be very careful when intending a force push!

The following listing illustrates how to force a push:

```shell
git push --force              # Force the push, regarding of the status of the remote branch
git push --force-with-lease   # Force the push only if all the remote changes have been pushed first
```
