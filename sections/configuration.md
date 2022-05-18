# Configuration

In this section we will see how to configure Git. The first topic will cover how to create a repository,
local or remote. We will talk about configuration files and look at aliases to make life easier.

## Creating a repository

In the previous section we talked about pushing and pulling modifications from a remote repository, but
we carefully ignored how this repository was created. Creating a repository is fairly straight-forward,
however how you create your repository has major implications.

Creating a local repository is done with the `init` command. It simply needs a path (existing or not).
This will create a `.git` folder in said path, which will become the root of the repository. The `.git`
folder will contain configuration files, references to local and remote branches, commit history and
so forth. In practice, aside from touching configuration files, there is no reason to ever go into
this folder.

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
remote would to have been created with the --bare option.

```shell
git remote -v                     # Lists remotes
git remote add <name> <url>       # Add a new remote
git remote-set-url <name> <url>   # Change the URL of given remote
```

## Configuration files

## Aliases