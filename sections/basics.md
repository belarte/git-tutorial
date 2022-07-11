# Basic usage

In this section, we will learn about the basic Git commands. These are all that is necessary for
a simple workflow on a single branch, such as *trunk base development*. 

The following assumes the existence of a remote repository.

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

In fact, `pull` is actually the combination of two commands. The first one is `fetch`, which will bring
the latest changes from remote (all commits, branches and tags) but it will not change anything locally.
The second command will be either `merge` or `rebase`, depending on the strategy selected, which will then
update your local copy with the latest remote changes.

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
git checkout -- <files>       # Exactly the same as 'git restore <files>' but deprecated use
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

## Push your local changes to a remote repository

Once you have one or more local commits, you can `push` them to a remote. By default, it will push the current
branch to the default remote, *e.g.* if you are on the main branch with the default remote, it will
push `main` to `origin`. You can specify which branch you want to push to which remote explicitly.

```shell
git push                    # Push current branch to default remote
git push <remote> <branch>  # Push given branch to given remote
```

## View diffs and previous commits

So far we talked about making modifications and communicating modifications to a remote. But what about viewing
those modifications before or after commit? Git has a few useful commands to help us view differences: `status`,
`log`, `diff` and `show`.

The `status` command simply shows you the list of files that have been modified since last commit. It separates
staged and unstaged files, so you can easily spot what is about to be committed. It also lists untracked files
and folders.

The `log` command shows the list of commits in reverse order, so you can see the most recent first. It shows a
large variety of information including commit message, hash, author's name and email.
By default, it is quite verbose, so you can tweak it with a few options, for instance `--oneline` shortens the
information to a single line or `--graph` shows you a graph of the branches involved in the latest history.

The `diff` command allows you to view the difference between two point in time. The most simple and common usage
is with no parameters at all, this will show you current local modifications that have not been staged or
committed yet. This is particularly useful to double-check your work when preparing a commit.
Using the `--cached` option you can see what has been staged already. You can also specify which files you
are interested in, or from and to which commits you want to see the diff.

```shell
git diff              # Show unstaged local modifications
git diff <files>      # Show unstaged local of given files
git diff --cached     # Show staged modifications
git diff <commit>     # Show diff between given commit and HEAD
git diff <from> <to>  # Show diff between two commits
git diff <branch>     # Show diff between current and given branch
```

The `show` command lets you inspect existing commits. Used without parameters it shows you the last commit.
You can specify the commit you want to show, using for instance its hash or a label (such as `HEAD` in the
following).

```shell
git show          # Show latest commit
git show HEAD     # Similar to previous example
git show HEAD~1   # Show second to last commit, the number can be changed to show previous commits
git show <hash>   # Show given commit
```

## How did we get here?

As an example, let us take a look at which Git commands have been run from the creation of the repository to
this exact moment! The repository is hosted on GitHub, so the UI was used for its creation. From there, all
Git commands have been run on the command line, using only commands presented in this very page.

```shell
git clone git@github.com:belarte/git-tutorial.git
cd git-tutorial
git status
git add .gitignore
git commit -m "Add gitignore and ignore .idea folder"
git config user.email <redacted>                          # Author was not configured correctly
git commit --amend --reset-author                         # So amending previous commit was necessary
git log
git status
git commit -am "Add introduction section and check-list for next steps"
git log
git push
git diff
git status
git add sections/basics.md
git add -u
git status
git commit -m "Add basics section and start filling with clone and pull"
git push
git status
git diff
git commit -am "Add section for committing modifications"
git status
git push
git status
git diff
git log
git diff sections/basics.md
git diff
git add -p
git diff --cached
git diff
git commit -m "Push and diff sections"
git push
git add -p
git commit -m "Add log and status section"
git push
```

We can see that a lot of time, I run commands to check the current status or the history, be that when I want
to commit or when I come back to the computer after a while.