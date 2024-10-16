---
title: Creating a Repository
teaching: 10
exercises: 0
---

::::::::::::::::::::::::::::::::::::::: objectives

- Create a local Git repository.
- Describe the purpose of the `.git` directory.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Where does Git store information?

::::::::::::::::::::::::::::::::::::::::::::::::::

Once Git is configured,
we can start using it.

To demonstrate the use of `git`, we will build a
[**data dictionary**](https://en.wikipedia.org/wiki/Data_dictionary) for the data we obtained
from UKHSA.

First, let's create a new directory in the `Desktop` folder for our work and then change the current working directory to the newly created one:

```bash
$ cd ~/Desktop
$ mkdir data-dictionary
$ cd data-dictionary
```

Then we tell Git to make `data-dictionary` a [repository](../learners/reference.md#repository)
-- a place where Git can store versions of our files:

```bash
$ git init
```

It is important to note that `git init` will create a repository that
can include subdirectories and their files -- there is no need to create
separate repositories nested within the `data-dictionary` repository, whether
subdirectories are present from the beginning or added later. Also, note
that the creation of the `data-dictionary` directory and its initialization as a
repository are completely separate processes.

If we use `ls` to show the directory's contents,
it appears that nothing has changed:

```bash
$ ls
```

But if we add the `-a` flag to show everything,
we can see that Git has created a hidden directory within `data-dictionary` called `.git`:

```bash
$ ls -a
```

```output
.	..	.git
```

Git uses this special subdirectory to store all the information about the project,
including the tracked files and sub-directories located within the project's directory.
If we ever delete the `.git` subdirectory,
we will lose the project's history.

Next, we will change the default branch to be called `main`.
This might be the default branch depending on your settings and version
of git.
See the [setup episode](08-setup.md#default-git-branch-naming) for more information on this change.

```bash
$ git checkout -b main
```

```output
Switched to a new branch 'main'
```

We can now start using one of the most important git commands, which is particularly helpful to beginners. `git status` tells us the status of our project, and better, a list of changes in the project and options on what to do with those changes. We can use it as often as we want, whenever we want to understand what is going on.

```bash
$ git status
```

```output
On branch main

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

If you are using a different version of `git`, the exact
wording of the output might be slightly different.

:::::::::::::::::::::::::::::::::::::::  challenge

## Places to Create Git Repositories

Along with tracking information about the data dictionary (the project we have already created),
we would also like to track information about related datasets.
Despite any concerns, we create a `related-data` project inside the `data-dictionary`
project with the following sequence of commands:

```bash
$ cd ~/Desktop   # return to Desktop directory
$ cd data-dictionary     # go into data-dictionary directory, which is already a Git repository
$ ls -a          # ensure the .git subdirectory is still present in the data-dictionary directory
$ mkdir related-data    # make a subdirectory data-dictionary/related-data
$ cd related-data       # go into related-data subdirectory
$ git init       # make the related-data subdirectory a Git repository
$ ls -a          # ensure the .git subdirectory is present indicating we have created a new Git repository
```

Is the `git init` command, run inside the `related-data` subdirectory, required for
tracking files stored in the `related-data` subdirectory?

:::::::::::::::  solution

## Solution

No. We do not need to make the `related-data` subdirectory a Git repository
because the `data-dictionary` repository can track any files, sub-directories, and
subdirectory files under the `data-dictionary` directory.  Thus, in order to track
all information about related data, we only needed to add the `related-data` subdirectory
to the `data-dictionary` directory.

Additionally, Git repositories can interfere with each other if they are "nested":
the outer repository will try to version-control
the inner repository. Therefore, it's best to create each new Git
repository in a separate directory. To be sure that there is no conflicting
repository in the directory, check the output of `git status`. If it looks
like the following, you are good to go to create a new repository as shown
above:

```bash
$ git status
```

```output
fatal: Not a git repository (or any of the parent directories): .git
```

:::::::::::::::::::::::::

## Correcting `git init` Mistakes

Now that we know that a nested repository is redundant and may cause confusion
down the road, we would like to go back to a single git repository. How can we undo
our last `git init` in the `related-data` subdirectory?

:::::::::::::::  solution

## Solution -- USE WITH CAUTION!

### Background

Removing files from a Git repository needs to be done with caution. But we have not learned
yet how to tell Git to track a particular file; we will learn this in the next episode. Files
that are not tracked by Git can easily be removed like any other "ordinary" files with

```bash
$ rm filename
```

Similarly a directory can be removed using `rm -r dirname`.
If the files or folder being removed in this fashion are tracked by Git, then their removal
becomes another change that we will need to track, as we will see in the next episode.

### Solution

Git keeps all of its files in the `.git` directory.
To recover from this little mistake, we can remove the `.git`
folder in the `related-data` subdirectory by running the following command from inside the `data-dictionary` directory:

```bash
$ rm -rf related-data/.git
```

But be careful! Running this command in the wrong directory will remove
the entire Git history of a project you might want to keep.
In general, deleting files and directories using `rm` from the command line cannot be reversed.
Therefore, always check your current directory using the command `pwd`.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- `git init` initializes a repository.
- Git stores all of its repository data in the `.git` directory.

::::::::::::::::::::::::::::::::::::::::::::::::::
