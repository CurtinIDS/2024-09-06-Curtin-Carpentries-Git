---
title: Creating a Repository
teaching: 10
exercises: 0
questions:
- "Where does Git store information?"
objectives:
- "Create a local Git repository."
- "Describe the purpose of the `.git` directory."
keypoints:
- "`git init` initializes a repository."
- "Git stores all of its repository data in the `.git` directory."
---

Once Git is configured, we can start using it.

We will help Nelle with her new project, create a repository with the data for north-pacific-gyre.

First, let's create a directory in `Desktop` folder for our work and then move into that directory:

~~~
$ cd ~/Desktop
$ mkdir north-pacific-gyre
$ cd north-pacific-gyre
~~~
{: .language-bash}

Then we tell Git to make `north-pacific-gyre` a [repository]({{ page.root }}{% link reference.md %}#repository)
-- a place where Git can store versions of our files:


~~~
$ git init
~~~
{: .language-bash}

It is important to note that `git init` will create a repository that
includes subdirectories and their files---there is no need to create
separate repositories nested within the `north-pacific-gyre` repository, whether
subdirectories are present from the beginning or added later. Also, note
that the creation of the `north-pacific-gyre` directory and its initialization as a
repository are completely separate processes.

If we use `ls` to show the directory's contents,
it appears that nothing has changed:

~~~
$ ls
~~~
{: .language-bash}

But if we add the `-a` flag to show everything,
we can see that Git has created a hidden directory within `north-pacific-gyre` called `.git`:

~~~
$ ls -a
~~~
{: .language-bash}

~~~
.	..	.git
~~~
{: .output}

Git uses this special subdirectory to store all the information about the project, 
including all files and sub-directories located within the project's directory.
If we ever delete the `.git` subdirectory,
we will lose the project's history.

Next, we will change the default branch to be called `main`.
This might be the default branch depending on your settings and version
of git.
See the [setup episode](02-setup.md) for more information on this change.

~~~
git checkout -b main
~~~
{: .language-bash}
~~~
Switched to a new branch 'main'
~~~
{: .output}


We can check that everything is set up correctly
by asking Git to tell us the status of our project:

~~~
$ git status
~~~
{: .language-bash}
~~~
On branch main

No commits yet

nothing to commit (create/copy files and use "git add" to track)
~~~
{: .output}

If you are using a different version of `git`, the exact
wording of the output might be slightly different.

> ## Places to Create Git Repositories
>
> Along with tracking information about north-pacific-gyre (the project we have already created),
> Nelle would also like to track her personal journal.
> Despite Jimmy's concerns, Nelle creates a `journal` project inside her `north-pacific-gyre`
> project with the following sequence of commands:
>
> ~~~
> $ cd ~/Desktop            # return to Desktop directory
> $ cd north-pacific-gyre   # go into north-pacific-gyre directory, which is already a Git repository
> $ ls -a                   # ensure the .git subdirectory is still present in the north-pacific-gyre directory
> $ mkdir journal           # make a sub-directory north-pacific-gyre/journal
> $ cd journal              # go into journal subdirectory
> $ git init                # make the journal subdirectory a Git repository
> $ ls -a                   # ensure the .git subdirectory is present indicating we have created a new Git repository
> ~~~
> {: .language-bash}
>
> Is the `git init` command, run inside the `journal` subdirectory, required for
> tracking files stored in the `journal` subdirectory?
>
> > ## Solution
> >
> > No. Nelle does not need to make the `journal` subdirectory a Git repository
> > because the `north-pacific-gyre` repository will track all files, sub-directories, and
> > subdirectory files under the `north-pacific-gyre` directory.  Thus, in order to track
> > all information about journal, Nelle only needed to add the `journal` subdirectory
> > to the `north-pacific-gyre` directory.
> >
> > Additionally, Git repositories can interfere with each other if they are "nested":
> > the outer repository will try to version-control
> > the inner repository. Therefore, it's best to create each new Git
> > repository in a separate directory. To be sure that there is no conflicting
> > repository in the directory, check the output of `git status`. If it looks
> > like the following, you are good to go to create a new repository as shown
> > above:
> >
> > ~~~
> > $ git status
> > ~~~
> > {: .language-bash}
> > ~~~
> > fatal: Not a git repository (or any of the parent directories): .git
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}
> ## Correcting `git init` Mistakes
> Jimmy explains to Nelle how a nested repository is redundant and may cause confusion
> down the road. Nelle would like to remove the nested repository. How can Nelle undo
> her last `git init` in the `journal` subdirectory?
>
> > ## Solution -- USE WITH CAUTION!
> >
> > ### Background
> > Removing files from a Git repository needs to be done with caution. But we have not learned 
> > yet how to tell Git to track a particular file; we will learn this in the next episode. Files 
> > that are not tracked by Git can easily be removed like any other "ordinary" files with
> > ~~~
> > $ rm filename
> > ~~~
> > {: .language-bash}
> >
> > Similarly a directory can be removed using `rm -r dirname` or `rm -rf dirname`.
> > If the files or folder being removed in this fashion are tracked by Git, then their removal 
> > becomes another change that we will need to track, as we will see in the next episode.
> >
> > ### Solution
> > Git keeps all of its files in the `.git` directory.
> > To recover from this little mistake, Nelle can just remove the `.git`
> > folder in the journal subdirectory by running the following command from inside the `north-pacific-gyre` directory:
> >
> > ~~~
> > $ rm -rf journal/.git
> > ~~~
> > {: .language-bash}
> >
> > But be careful! Running this command in the wrong directory will remove
> > the entire Git history of a project you might want to keep.
> > Therefore, always check your current directory using the command `pwd`.
> {: .solution}
{: .challenge}
