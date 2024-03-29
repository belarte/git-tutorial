# Git tutorial

A tutorial to learn Git basics.

---

## What we will talk about

In this tutorial, we will learn about the most common Git commands and practices.
We will talk about the most basic things such as committing changes locally, pushing to
and pulling from a remote repository. We will also cover more advanced techniques such as
branches management (creating and merging) or rebasing to rewrite history.

The following check-list presents the things already covered and what will come next:

- [x] [Basic usage](sections/basics.md)
  - [x] Clone
  - [x] Pull
  - [x] Commit
  - [x] Push
  - [x] Diffs
- [x] [Repository configuration](sections/configuration.md)
  - [x] Creating a repository
  - [x] Configuration files
- [x] [Managing branches](sections/branching.md)
  - [x] What is a branch?
  - [x] Creating branches
  - [x] Cherry-picking
  - [x] Merging
  - [x] Solving conflicts
- [x] [Rewriting history](sections/history.md)
  - [x] Undoing local changes
  - [x] Rebasing
- [x] [Exercises](sections/exercises.md)
- [ ] Closing notes

This tutorial will also contain exercises to apply the practices presented here.
Trying the exercises will require cloning the repository. But more on that later!

## Not covered here

In this tutorial, we will not cover the following:

- :x: Pull requests
- :x: Configuring GitHub
- :x: Integration with IDEs

Pull requests are more of a workflow than a Git practice, and it relies on a UI.
Pull requests are meant for asynchronous code reviews, they are widely used in the open source
community but are far from optimal in the context of a Team working closely together.

We will also not cover vendor specific things, such as GitHub or GitLab, or integration with IDEs.
Though GitHub is widely used (as demonstrated with this project), we might cover it at a later stage
