---
title: Conflicts
teaching: 15
exercises: 0
questions:
- "What do I do when my changes conflict with someone else's?"
objectives:
- "Explain what conflicts are and when they can occur."
- "Resolve conflicts resulting from a merge."
keypoints:
- "Conflicts occur when two or more people change the same lines of the same file."
- "The version control system does not allow people to overwrite each other's changes blindly, but highlights conflicts so that they can be resolved."
---
> ## Practicing By Yourself
> 
> In some projects you will work with other people and make changes simultaneously that will sometimes conflict with each other.
> Using git via GitHub means that you see your collaborators changes as "changes on the remote repository".
> In this lesson you'll play the role of *you* and also *collaborator* by making changes on your local repo and also on the remote (GitHub) repo.
{: .callout}


As soon as people can work in parallel, they'll likely step on each other's
toes.  This will even happen with a single person: if we are working on
a piece of software on both our laptop and a server in the lab, we could make
different changes to each copy.  Version control helps us manage these
[conflicts]({{ page.root}}{% link reference.md %}#conflict) by giving us tools to
[resolve]({{ page.root }}{% link reference.md %}#resolve) overlapping changes.

To see how we can resolve conflicts, we must first create one.  The file
`goostats.sh` currently looks like this in both copies of our `north-pacific-gyre`
repository:

~~~
$ cat goostats.sh
~~~
{: .language-bash}

~~~
# Load a given file
fname=$1
echo "Working with ${fname}"
echo "Welcome to Nelle's stats script"
# Compute the min/max/range of values in a file
~~~
{: .output}

Let's add a line to the version that is on github.
Navigate to your repository on github and choose the file `goostats.sh` and then make the following changes with the online editor:

~~~
# Load a given file
fname=$1
echo "Working with ${fname}"
echo "Welcome to Nelle's stats script"
# Compute the min/max/range of values in a file
min=$( cat ${fname} | sort | head -1)
~~~
{: .output}

Save these changes with the commit message of "Calculate first part of stats".
Under the hood, github is doing the following commands for you:

~~~
$ git add goostats.sh
$ git commit -m "Calculate first part of stats"
~~~
{: .language-bash}


Now let's modify the same file in the local repository:

~~~
$ nano goostats.sh
$ cat goostats.sh
~~~
{: .language-bash}

~~~
# Load a given file
fname=$1
echo "Working with ${fname}"
echo "Welcome to Nelle's stats script"
# Compute the min/max/range of values in a file
max=$( cat ${fname} | sort | tail -1)
~~~
{: .output}

We can commit the change locally:

~~~
$ git add goostats.sh
$ git commit -m "Add first step for stats"
~~~
{: .language-bash}

~~~
[main 07ebc69] Add first step for stats
 1 file changed, 1 insertion(+)
~~~
{: .output}

but Git won't let us push it to GitHub:

~~~
$ git push origin main
~~~
{: .language-bash}

~~~
To https://github.com/nelle/north-pacific-gyre.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/nelle/north-pacific-gyre.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
~~~
{: .output}

![The Conflicting Changes]({{page.root}}{% link fig/conflict.svg %})

Git rejects the push because it detects that the remote repository has new updates that have not been
incorporated into the local branch.
What we have to do is pull the changes from GitHub,
[merge]({{ page.root }}{% link reference.md %}#merge) them into the copy we're currently working in, and then push that.
Let's start by pulling:

~~~
$ git pull origin main
~~~
{: .language-bash}

~~~
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 3 (delta 2), reused 3 (delta 2), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/nelle/north-pacific-gyre
 * branch            main     -> FETCH_HEAD
    29aba7c..dabb4c8  main     -> origin/main
Auto-merging goostats.sh
CONFLICT (content): Merge conflict in goostats.sh
Automatic merge failed; fix conflicts and then commit the result.
~~~
{: .output}

The `git pull` command updates the local repository to include those
changes already included in the remote repository.
After the changes from remote branch have been fetched, Git detects that changes made to the local copy 
overlap with those made to the remote repository, and therefore refuses to merge the two versions to
stop us from trampling on our previous work. The conflict is marked in
in the affected file:

~~~
$ cat goostats.sh
~~~
{: .language-bash}

~~~
# Load a given file
fname=$1
echo "Working with ${fname}"
echo "Welcome to Nelle's stats script"
# Compute the min/max/range of values in a file
<<<<<<< HEAD
max=$( cat ${fname} | sort | tail -1)
=======
min=$( cat ${fname} | sort | head -1)
>>>>>>> dabb4c8c450e8475aee9b14b4383acc99f42af1d
~~~
{: .output}

Our change is preceded by `<<<<<<< HEAD`.
Git has then inserted `=======` as a separator between the conflicting changes
and marked the end of the content downloaded from GitHub with `>>>>>>>`.
(The string of letters and digits after that marker
identifies the commit we've just downloaded.)

It is now up to us to edit this file to remove these markers
and reconcile the changes.
We can do anything we want: keep the change made in the local repository, keep
the change made in the remote repository, write something new to replace both,
or get rid of the change entirely.
Let's use both so that the file looks like this:

~~~
$ cat goostats.sh
~~~
{: .language-bash}

~~~
# Load a given file
fname=$1
echo "Working with ${fname}"
echo "Welcome to Nelle's stats script"
# Compute the min/max/range of values in a file
min=$( cat ${fname} | sort | head -1)
max=$( cat ${fname} | sort | tail -1)
~~~
{: .output}

To finish merging,
we add `goostats.sh` to the changes being made by the merge
and then commit:

~~~
$ git add goostats.sh
$ git status
~~~
{: .language-bash}

~~~
On branch main
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:

	modified:   goostats.sh

~~~
{: .output}

~~~
$ git commit -m "Merge changes from GitHub"
~~~
{: .language-bash}

~~~
[main 2abf2b1] Merge changes from GitHub
~~~
{: .output}

Now we can push our changes to GitHub:

~~~
$ git push origin main
~~~
{: .language-bash}

~~~
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 8 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 645 bytes | 645.00 KiB/s, done.
Total 6 (delta 4), reused 0 (delta 0)
remote: Resolving deltas: 100% (4/4), completed with 2 local objects.
To https://github.com/nelle/north-pacific-gyre.git
   dabb4c8..2abf2b1  main -> main
~~~
{: .output}

Git keeps track of what we've merged with what,
so we don't have to fix things by hand again
when the collaborator who made the first change pulls again:

~~~
$ git pull origin main
~~~
{: .language-bash}

~~~
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 6 (delta 4), reused 6 (delta 4), pack-reused 0
Unpacking objects: 100% (6/6), done.
From https://github.com/nelle/north-pacific-gyre
 * branch            main     -> FETCH_HEAD
    dabb4c8..2abf2b1  main     -> origin/main
Updating dabb4c8..2abf2b1
Fast-forward
 goostats.sh | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
~~~
{: .output}

We get the merged file:

~~~
$ cat goostats.sh
~~~
{: .language-bash}

~~~
# Load a given file
fname=$1
echo "Working with ${fname}"
echo "Welcome to Nelle's stats script"
# Compute the min/max/range of values in a file
min=$( cat ${fname} | sort | head -1)
max=$( cat ${fname} | sort | tail -1)
~~~
{: .output}

We don't need to merge again because Git knows someone has already done that.

Git's ability to resolve conflicts is very useful, but conflict resolution
costs time and effort, and can introduce errors if conflicts are not resolved
correctly. If you find yourself resolving a lot of conflicts in a project,
consider these technical approaches to reducing them:

- Pull from upstream more frequently, especially before starting new work
- Use topic branches to segregate work, merging to main when complete
- Make smaller more atomic commits
- Where logically appropriate, break large files into smaller ones so that it is
  less likely that two authors will alter the same file simultaneously
- For files where you are writing text, keep one line per sentence and don't hard-wrap lines. This means changing 1 sentence changes only 1 line in your file

Conflicts can also be minimized with project management strategies:

- Clarify who is responsible for what areas with your collaborators
- Discuss what order tasks should be carried out in with your collaborators so
  that tasks expected to change the same lines won't be worked on simultaneously
- If the conflicts are stylistic churn (e.g. tabs vs. spaces), establish a
  project convention that is governing and use code style tools (e.g.
  `htmltidy`, `perltidy`, `rubocop`, etc.) to enforce, if necessary


> ## Conflicts on Non-textual files
>
> What does Git do
> when there is a conflict in an image or some other non-textual file
> that is stored in version control?
>
> > ## Solution
> >
> > Let's try it. Suppose Nelle takes a picture of a whale and
> > calls it `whale.jpg`.
> >
> > If you do not have an image file of whale available, you can create
> > a dummy binary file like this:
> >
> > ~~~
> > $ head --bytes 1024 /dev/urandom > whale.jpg
> > $ ls -lh whale.jpg
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > -rw-r--r-- 1 nelle 57095 1.0K Mar  8 20:24 whale.jpg
> > ~~~
> > {: .output}
> >
> > `ls` shows us that this created a 1-kilobyte file. It is full of
> > random bytes read from the special file, `/dev/urandom`.
> >
> > Now, suppose Nelle adds `whale.jpg` to her repository:
> >
> > ~~~
> > $ git add whale.jpg
> > $ git commit -m "Add picture of whale"
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > [main 8e4115c] Add picture of whale
> >  1 file changed, 0 insertions(+), 0 deletions(-)
> >  create mode 100644 whale.jpg
> > ~~~
> > {: .output}
> >
> > Suppose that Jimmy has added a similar picture in the meantime.
> > Hers is a picture of a whale with calf, but it is *also* called `whale.jpg`.
> > When Nelle tries to push, she gets a familiar message:
> >
> > ~~~
> > $ git push origin main
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > To https://github.com/nelle/north-pacific-gyre.git
> >  ! [rejected]        main -> main (fetch first)
> > error: failed to push some refs to 'https://github.com/nelle/north-pacific-gyre.git'
> > hint: Updates were rejected because the remote contains work that you do
> > hint: not have locally. This is usually caused by another repository pushing
> > hint: to the same ref. You may want to first integrate the remote changes
> > hint: (e.g., 'git pull ...') before pushing again.
> > hint: See the 'Note about fast-forwards' in 'git push --help' for details.
> > ~~~
> > {: .output}
> >
> > We've learned that we must pull first and resolve any conflicts:
> >
> > ~~~
> > $ git pull origin main
> > ~~~
> > {: .language-bash}
> >
> > When there is a conflict on an image or other binary file, git prints
> > a message like this:
> >
> > ~~~
> > $ git pull origin main
> > remote: Counting objects: 3, done.
> > remote: Compressing objects: 100% (3/3), done.
> > remote: Total 3 (delta 0), reused 0 (delta 0)
> > Unpacking objects: 100% (3/3), done.
> > From https://github.com/nelle/north-pacific-gyre.git
> >  * branch            main     -> FETCH_HEAD
> >    6a67967..439dc8c  main     -> origin/main
> > warning: Cannot merge binary files: whale.jpg (HEAD vs. 439dc8c08869c342438f6dc4a2b615b05b93c76e)
> > Auto-merging whale.jpg
> > CONFLICT (add/add): Merge conflict in whale.jpg
> > Automatic merge failed; fix conflicts and then commit the result.
> > ~~~
> > {: .output}
> >
> > The conflict message here is mostly the same as it was for `goostats.sh`, but
> > there is one key additional line:
> >
> > ~~~
> > warning: Cannot merge binary files: whale.jpg (HEAD vs. 439dc8c08869c342438f6dc4a2b615b05b93c76e)
> > ~~~
> > {: .output}
> >
> > Git cannot automatically insert conflict markers into an image as it does
> > for text files. So, instead of editing the image file, we must check out
> > the version we want to keep. Then we can add and commit this version.
> >
> > On the key line above, Git has conveniently given us commit identifiers
> > for the two versions of `whale.jpg`. Our version is `HEAD`, and Jimmy's
> > version is `439dc8c0...`. If we want to use our version, we can use
> > `git checkout`:
> >
> > ~~~
> > $ git checkout HEAD whale.jpg
> > $ git add whale.jpg
> > $ git commit -m "Use image of just whale instead of with calf"
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > [main 21032c3] Use image of just whale instead of with calf
> > ~~~
> > {: .output}
> >
> > If instead we want to use Jimmy's version, we can use `git checkout` with
> > Jimmy's commit identifier, `439dc8c0`:
> >
> > ~~~
> > $ git checkout 439dc8c0 whale.jpg
> > $ git add whale.jpg
> > $ git commit -m "Use image of whale with calf instead of just whale"
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > [main da21b34] Use image of whale with calf instead of just whale
> > ~~~
> > {: .output}
> >
> > We can also keep *both* images. The catch is that we cannot keep them
> > under the same name. But, we can check out each version in succession
> > and *rename* it, then add the renamed versions. First, check out each
> > image and rename it:
> >
> > ~~~
> > $ git checkout HEAD whale.jpg
> > $ git mv whale.jpg whale-only.jpg
> > $ git checkout 439dc8c0 whale.jpg
> > $ mv whale.jpg whale-calf.jpg
> > ~~~
> > {: .language-bash}
> >
> > Then, remove the old `whale.jpg` and add the two new files:
> >
> > ~~~
> > $ git rm whale.jpg
> > $ git add whale-only.jpg
> > $ git add whale-calf.jpg
> > $ git commit -m "Use two images: just whale and with calf"
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > [main 94ae08c] Use two images: just whale and with calf
> >  2 files changed, 0 insertions(+), 0 deletions(-)
> >  create mode 100644 whale-calf.jpg
> >  rename whale.jpg => whale-only.jpg (100%)
> > ~~~
> > {: .output}
> >
> > Now both images of whale are checked into the repository, and `whale.jpg`
> > no longer exists.
> {: .solution}
{: .challenge}

> ## A Typical Work Session
>
> You sit down at your computer to work on a shared project that is tracked in a
> remote Git repository. During your work session, you take the following
> actions, but not in this order:
>
> - *Make changes* by appending the number `100` to a text file `numbers.txt`
> - *Update remote* repository to match the local repository
> - *Celebrate* your success with some fancy beverage(s)
> - *Update local* repository to match the remote repository
> - *Stage changes* to be committed
> - *Commit changes* to the local repository
>
> In what order should you perform these actions to minimize the chances of
> conflicts? Put the commands above in order in the *action* column of the table
> below. When you have the order right, see if you can write the corresponding
> commands in the *command* column. A few steps are populated to get you
> started.
>
> |order|action . . . . . . . . . . |command . . . . . . . . . . |
> |-----|---------------------------|----------------------------|
> |1    |                           |                            |
> |2    |                           | `echo 100 >> numbers.txt`  |
> |3    |                           |                            |
> |4    |                           |                            |
> |5    |                           |                            |
> |6    | Celebrate!                | `AFK`                      |
>
> > ## Solution
> >
> > |order|action . . . . . . |command . . . . . . . . . . . . . . . . . . . |
> > |-----|-------------------|----------------------------------------------|
> > |1    | Update local      | `git pull origin main`                     |
> > |2    | Make changes      | `echo 100 >> numbers.txt`                    |
> > |3    | Stage changes     | `git add numbers.txt`                        |
> > |4    | Commit changes    | `git commit -m "Add 100 to numbers.txt"`     |
> > |5    | Update remote     | `git push origin main`                     |
> > |6    | Celebrate!        | `AFK`                                        |
> >
> {: .solution}
{: .challenge}
