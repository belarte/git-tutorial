# Basic usage

In this section, we will learn about the basic Git commands. These are all that is necessary for
a simple workflow on a single branch, such as *trunk base development*. 

## Clone a repository

The first step to work on a Git repository is to `clone` it. This simple command takes the address
of the repository (via HTTPS or SSH for instance) and will check out the main branch (usually
conveniently named `main`) at its latest revision.

```shell
git clone <repository>
```

## Pull the latest changes

Once a repository has been cloned, if you want to work on an up-to-date version of the code,
you will need to frequently `pull` the latest changes. This is mandatory if you want to push
changes. A few options are available to help you deal with local modifications.

If you have local commits, Git will by default merge those to the remote branch and create a
merge commit. However, you can change this strategy to do a rebase instead. This will have
the effect of putting your changes on top of the remote changes, giving you a linear history.

If you have uncommitted changes, Git will prevent you from pulling. You will need to `stash` said
changes. You can pass an option to `pull` to stash and un-stash the local changes automatically.

```shell
git pull
git pull --rebase               # Rebase local commits on top of remote branch
git pull --autostash            # Stash uncommitted changes before pull and pop the stash after
git pull --rebase --autostash   # Options can be combined
git stash                       # Stash uncommitted changes
git stash pop                   # Pop previously stashed changes
```