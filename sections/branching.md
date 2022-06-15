# Managing branches

In this section, we will discover what a branch is, how to manage them and the most usual techniques to port
modifications from one branch to another.

We have been using branches from the beginning of this tutorial, as `main` is indeed a branch. By convention,
`main` is the first and central branch in a Git repository. It was historically called `master` but was changed in
recent years. And going back further in time, in `SVN` the main branch was called `trunk`, hence the expression *trunk
based development*.

## What is a branch?

Before presenting what a branch is, we first need to introduce the concept of `reference`. So far we have identified
commits only by their hash number. This is the default way of identifying different modifications. But there are other
ways of identifying a single commit or a chain of commits (as in a succession of commits, *a.k.a.* a branch).

A reference is a label attached to a commit. If you think programmatically, a reference is just a pointer to a value
(the hash of the commit it points to). The two main references types are `branches` and `tags`. Branches and tags are
very similar, with one major difference: a tag is a reference attached to a single commit in history and will not
change. A branch is a reference to the latest commit on a chain and will be updated after every commit made on said
branch. 

There is a special reference named `HEAD` which points to the last commit of the current branch. `HEAD` can be used just
like any other reference. It also allows you to reference previous commits. For instance:

```shell
git show HEAD     # Shows the latest commit on the current branch
git show HEAD~1   # Shows the second to last commit on the current branch
git show HEAD~n   # Shows the nth previous commit on the current branch
```

References can be created at any point in time, referring to any commit in history. References can be created from
other references and so on, and references can be deleted. One thing to remember is that references are always created
locally. If you want to share references, you will need to explicitly push them to a remote. This also applies if you
want to delete a reference.

References are stored in `.git/refs`. It contains a hierarchy of folders, with at the end files that contains the hash
of the commit it points to.

Before diving into the specifics, there is one special command that is worth mentioning. `reflog` lets you access
almost everything you have done on a repository (be that a commit, merge or pull for instance). It acts as a history
of the local repository and gives you the option to access said history. This might be particularly helpful in case
something very bad happen during a merge.

## Creating a branch

## Cherry-picking

## Merging

At first sight, merging can look quite a daunting task, but there are tools and techniques to help. 

Let's first talk about different merging techniques. They can be grouped in two categories: *fast-forward* and
*three-way* merges. Fast-forward merges are simpler and allow one to keep a linear history. Three-way merges are
intrinsically harder to do as both branches have evolved differently and might require solving conflicts.

Merging branches is done with the `merge` command. By default, it takes a branch as the argument, and will merge said
branch to the current one. Merging is only possible if the working copy is clear, meaning there cannot be modified or
uncommitted files. You either need to commit said changes, stash them or revert them.

```shell
git merge <branch>   # Merge given branch to with the current one
```

### Fast-forward merge

A fast-forward merge is possible when the branch to merge is further in history than the destination branch. This is for
example the case if you branch from main, do a few commits and then want to merge back to main while main has not
changed. In that case, Git is smart enough to move the reference of the destination branch to the commit of the target
branch. This will combine the histories of both branches into one, effectively maintaining a linear history. The commits
will be kept untouched, as no modifications are necessary. No conflicts can happen as the branches have not diverged.

If the destination branch have changed, a fast-forward merge is still possible, but requires first a `rebase`. If both
branches have evolved from the branching point, you can first rebase the target branch onto the destination branch. This
will in re-apply all commits from the target branch onto the destination branch, one by one in chronological order. As
both branches have changed, conflicts might happen. Git can automatically solve simple conflicts, but not always. In
that situation, the rebase will stop as the conflicting commit and prompts you to solve the conflict by hand before
continuing. You will need to edit the incriminated files, mark them as *solved* (with the `add` command) and then tell
Git to continue the rebase. Doing so will generate a new commit (as files have been modified in the process) and the
subsequent commits will also be re-generated, as they will then be based on a different commit. During a rebase, each
individual commit can create conflicts and will have to be solved individually. This is actually a good thing, as it is
easier to solve than a *big-bang merge* which is what would have happened in the case of a *three-way* merge.

```shell
git rebase <branch>       # Rebase given branch on top of the current branch
git add <files>           # Once conflicts have been manually solved, you need to add incriminated files
git rebase --continue     # Once conflicts are solved and files added, tell git to continue the rebase
git rebase --abort        # When a conflict happens, you can abort the whole process
```

### Three-way merge

## How did we get here?

Let's take a quick look at the command that were run to produce this section. To avoid cluttering the listings, I
omitted all commands related to logging and viewing differences.

The first listing describes commits done on main before any branches or tags were introduced.

```shell
git add -p
git add sections/branching.md
git commit -m "Describe what is a reference"
git push
git commit -m "Add 'how did we get here' section in branching" sections/branching.md
git push
```

The forth listing describes how to merge branches in a clean way.

```shell
git switch -c merging-branches start-branching
git push --set-upstream origin merging-branches
```