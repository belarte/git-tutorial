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

Let's take a look at how to create tags and branches, stating with the former.

### Working with tags

As mentioned in the previous section, a tag is immutable. Once created it will always point to the same commit. For
this reason, they are mostly used to keep track of the state of the repository at a given point in time. For instance,
it can be quite useful to tag releases.

The `tag` command is used for most operations related to tags, starting by listing them. With no options or arguments
the command will list all existing tags.

There are two types of tags: `annotated tags` and `lightweight tags`. Annotated tags are created with a tagging message
and will store some information, including the date and the identity of the author. Lightweight tags will only store the
hash of the commit it refers to and nothing else.

Tags can be created retrospectively, you do not need to be at the commit you want to tag. Doing so only requires a
commit hash or another reference when creating the tag.

The tag command will create local tags. To share them with a remote, you will need to `push` them. After it has been
pushed, anyone who pulls from the repository will have access to the new tags. Tags deletion works in a similar fashion.
You need to delete tags locally and remotely with separate commands.

```shell
git tag                                       # List existing tags
git tag -l "<regex>"                          # List existing tags matching the given regular expression
git tag -a <name> -m <message>                # Create an annotated tag at the current position with the given message
git tag -a <name>                             # Create an annotated tag at the current position, will prompt for a message
git tag -a <name> -m <message> <hash or ref>  # Create an annotated tag at the given position with the given message
git tag <name>                                # Create a lightweight tag with the given name
git push <remote> <name>                      # Push the given tag to the given remote
git push <remote> --tags                      # Push all tags to the given remote
git tag -d <name>                             # Delete a local tag
git push <remote> --delete <name>             # Delete a remote tag 
```

## Cherry-picking

## Merging

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

The second listing describes how to create a tag, a branch and do a fast-forward merge.

```shell
git tag -a start-branching -m "Starting point for the exercises related to branching and merging"
git switch -c create-branches
git push origin start-branching
git commit -am "Section on tags"
```