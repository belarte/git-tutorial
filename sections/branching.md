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