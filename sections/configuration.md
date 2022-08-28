# Configuration

In this section we will see how to configure Git. The first topic will cover how to create a repository,
local or remote. We will talk about configuration files and look at aliases to make life easier.

## Creating a repository

In the previous section we talked about pushing and pulling modifications from a remote repository, but
we carefully ignored how this repository was created. Creating a repository is fairly straight-forward,
however how you create your repository has major implications.

Creating a local repository is done with the `init` command. It simply needs a path (existing or not).
As this repository is local, it does not have a remote.
This will create a `.git` folder in said path, which will become the root of the repository. The `.git`
folder will contain configuration files, references to local and remote branches, commit history and
so forth. In practice, there is no reason to ever go into this folder, as every file in there can be
updated from the command line. We will see a few examples of how to do that later on.

The root folder becomes the working copy of the repository. From this point you can add files and
commit them as described in the previous section. You could even clone this repository and pull from it.
However, you cannot push to it. Well, you can but after some workarounds, but it is not recommended.
The reason is that pushing to this repository can mess with the working copy.

For that reason, if you need to share a repository with several people or just have a remote save of
your work, you need to create a repository for that purpose. This is done with the `--bare` option of
the init command. This will not create the `.git` folder but rather use the root of the project to put
all the Git related files. Thus, there will not be and cannot be a working copy in a bare repository.
Usually such a repository will be created on a remote machine everyone involved has access to. The
obvious example is GitHub. When creating a new project with the UI, it will create a bare repository in
the cloud. After cloning it you will have a working copy on your local machine.

```shell
git init <folder>           # Create a local repository, with a .git folder and a working copy
git init --bare <folder>    # Create a bare repository
```

It is possible to have created a local repository and later on add a remote you can push to. Though this
remote would to have been created with the `--bare` option. These commands will update the configuration file
listing all the branches and remotes.

```shell
git remote -v                     # Lists remotes
git remote add <name> <url>       # Add a new remote
git remote-set-url <name> <url>   # Change the URL of given remote
```

## Configuration files

Git behaviour is defined by configurations files, most of which are located in the `.git` folder located in
the working copy of the local repository. The one file that allows customisation is `.git/config`. All the
others are Git internal machinery and should not be touched. The following snippet is the `.git/config`
file of the current project.

```text
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
[remote "origin"]
    url = git@github.com:belarte/git-tutorial.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
    remote = origin
    merge = refs/heads/main
[user]
    email = <redacted for obvious reason>
```

The core section defines some general configurations. You can see there explicitly this repository is not
a bare repository. We then have two sections about remotes and branches.
In here you can see a single remote `origin` and a single branch `main`. The last section is about user
information. Remember that the user's name and email address are  recommended to sign commits, it might
even be enforced by some systems.

You might notice that my user's name is not defined here. This is because I defined it in a global configuration
file, shared with all git repository on my machine. This file allows one to have common configurations (which
avoids duplication when you have multiple repositories checked out) and still let you add local configurations
when appropriate. The reason I did it here with my user's details is because my name will never change, hence
the global configuration, but my email address may vary depending if I am working on a work related or personal
project.

The following snippet is the global configuration file, located at `~/.gitconfig` on my machine.

```text
[user]
    name = Bruno Belarte
[alias]
    st = status
    amend = commit --amend --no-edit
    lg = log --oneline --decorate --graph --color --pretty=format:'%C(red)%h%C(reset) -%C(yellow)%d%C(reset) %s %C(green)(%cr) %C(cyan)<%an>%C(reset)' --abbrev-commit
    sh = show --ext-diff
[pull]
    rebase = true
[diff]
    external = difft --background=light --color=always
```

The user section is similar to the one in the previous file but holds different fields. The alias section defines a few
aliases of mine, shared between all the repository on my computer. You can notably see I have a few shorter aliases for
frequent commands, and a more convenient log command that will show commits on one line and can show branches as graph.
The pull section is configured to use rebase as the default strategy. The diff section configure an external diff tool.

None of these sections are mandatory. They are only there because I run some configuration commands from the command
line using the `--global` flag. As stated previously, editing the files manually is possible, but I would recommend
using the command line instead, be that to add an alias or modify a branch or a remote.

Here are a few examples of how to modify the configurations, local and global.

```shell
git config --global user.name "Firstname Lastname"  # Set user name as a global configuration
git config user.email "email@address.com"           # Set user email as a local configuration
git config --global alias.<new alias> <command>     # How to define new global aliases
git config --global alias.sh "show --ext-diff"      # Example of an alias as seen in the global configuration file above
git config --global pull.rebase true                # Set rebase as the default strategy when pulling
```
