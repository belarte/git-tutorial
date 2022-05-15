# Basic usage

In this section, we will learn about the basic Git commands. These are all that is necessary for
a simple workflow on a single branch, such as *trunk base development*. 

## Clone a repository

The first step to work on a Git repository is to `clone` it. This simple command takes the address
of the repository (via HTTPS or SSH for instance) and will check out the main branch (usually
conveniently named `main`) at its latest revision. It will keep track of the remote repository,
by default labelling it `origin`. You can keep track of multiple remote repositories and push/pull
to them individually.

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
git pull                        # Pull changes from default remote to current branch
git pull <remote> <branch>      # Explicitly pulls given branch from given remote and merge changes locally
git pull --rebase               # Rebase local commits on top of remote branch
git pull --autostash            # Stash uncommitted changes before pull and pop the stash after
git pull --rebase --autostash   # Options can be combined
git stash                       # Stash uncommitted changes
git stash pop                   # Pop previously stashed changes
```

## Commit your work locally

To save your work and create a local history, you mainly need two commands: `add` and `commit`.
The command `restore` can be used to undo some changes. Let's look at these three commands in details.

### Staging changes

When you are ready to save your changes, first you need to `add` them to the list of staged changes.
You can either add the files individually or add folders. This is necessary if the files are not
already tracked. You can also use `add -u` to automatically add already tracked files.

Adding a file is not *all or nothing*. With the command `add -p` you can choose which individual
modifications you want to add.

| :warning: Adding whole folders will add ***every*** file in that folder that is not explicitly ignored! |
|---------------------------------------------------------------------------------------------------------|

```shell
git add <files>   # Add listed files to the list of changes to commit
git add -u        # Add all already tracked files to the list of changes to commit
git add -p        # Interactively asks you which modifications you want to add
```

### Unstaging changes

The `restore` command helps you undo local modifications. Used without options, it will discard
all changes and revert the given files to their last committed version. Used with the `--staged`
option, it will unstage changes that have already been added without modifying the files.

A quick word about the `checkout` command: historically, the checkout command had too many
responsibilities and was deemed too complicated for newcomers. It has been decided in recent
years to split its responsibilities into several commands. `restore` is one of these new commands,
it simply restores a file to a previous state. We will see more of those new commands later.

```shell
git restore <files>           # Restore file to last committed version
git restore --staged <files>  # Unstage changes
git checkout -- <files>       # Exactly the same as 'git restore <files' but deprecated use
```

### Committing changes

The `commit` command creates a local commit with the staged modifications. The new commit will be
identified by a unique hash number. This hash can then be used to identify the commit across branches
for merging or rebasing for instance. Each commit is signed with its author's name and email address
and require a message which should ideally describe the content of the commit. You can pass the message
to the commit command directly with the `-m` option. If you do not, Git will open the default text
editor and wait for you to enter a message.

Git allows you to add files to be committed with the commit command directly. You can either use the
`-a` option which will commit all tracked files (similar to running `git add -u` beforehand), or
explicitly specify the files to be committed.

```shell
git commit                                    # Commit staged files, open text editor to enter commit message
git commit -m "Describe your commit"          # Commit staged files with given message
git commit <files>                            # Commit given files, open text editor to enter commit message
git commit <files> -m "Describe your commit"  # Commit given files with given message
git commit -a                                 # Commit all modifications to all tracked files, open text editor to enter commit message
git commit -am "Describe your commit"         # Commit all modifications to all tracked files with given message
```

Once a set of modifications have been committed, they are not set in stone. You can amend the last
commit easily with the `--amend` option of the commit command. After staging the new modifications,
run the commit command again with this option. This will generate a new commit with a new hash.

```shell
git commit --amend                # Add staged modifications to previous commit, prompting for a commit message
git commit -a --amend             # Modify previous commit with all modifications to all tracked files, prompting for a commit message
git commit -a --amend --no-edit   # Modify previous commit with all modifications to all tracked files, reuse previous commit message
git commit --amend --reset-author # Update the author's name and email address of the last commit
```