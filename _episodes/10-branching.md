---
title: Branches
teaching: 15
exercises: 0
questions:
- "How can I used branches to develop ideas in parallel?"
objectives:
- "Understand branches and how to use them."
- "Create and merge branches."
keypoints:
- "Branches allow you to work on multiple parts of your repo at once, without disrupting the main/working version."
- "Branches are very helpful for both solo and collaborative work."
---

## Branching
The term 'branch' has been mentioned a few times in the previous lessons, but we didn't describe what branches are.
In git terminology a *branch* is one of the (possibly many) end points on a path that is describe by the commits of your repository.
Until now our repository has been a linear chain of commits, where each commit builds on the previous one like this:
![Git linear branch](https://wac-cdn.atlassian.com/dam/jcr:547aa16b-4bdd-45bc-9fbc-18e795dd9df1/02%20Creating%20branches.svg?cdnVersion=1156)

Note that in the above we represent commits as circles (read left to right), and the "Main" branch of our repository is just a pointer to a given commit along this chian.


However, at any point in our history, we can have two commits, that are both related to the same parent, essentially making a "branch" in our chain of commits.
This means that we can have multiple end points to our chain of commits.
Why would we want to do this?
A common reason is related to the following diagram:
![Git three branches](https://wac-cdn.atlassian.com/dam/jcr:a905ddfd-973a-452a-a4ae-f1dd65430027/01%20Git%20branch.svg?cdnVersion=1156v)

In the branching example above we have three different end points to our chain labelled "Little Feature", "Main", and "Big Feature".
As the name suggests each branch serves a particular purpose.
When you want to add a new feature or fix a bug you create a new branch to encapsulate your changes.
Having multiple branches, and being able to swap between them, means that you can effectively have multiple version of your code in development at once.
This might sound scary but you can, for example, have a main branch that is "always working", and a development branch that "should work", and feature branches that are "not working yet".
The thing that makes branches so useful is that you can merge branches onto each other.
So when your feature works, you can copy all the commits onto the development branch at once.
This is called merging, and it's really powerful.
There is of course a risk of causing conflicts.

## Create a branch
In our `north-pacific-gyre` repository create a new branch called 'compute-range' as follows:
~~~
git branch compute-range
git checkout compute-range
~~~
{: .language-bash}

Within this branch we can try to write the code that will compute the range of the values in a given file.
For now lets add the following code to the last line of `goostats.sh`:
~~~
range=$( echo "${max}-${min}" | bc -l)
~~~
{: .language-bash}

Commit these changes to the `compute-range` branch, with an appropriate message.
~~~
git add goostats.sh
git commit -m 'Compute the range of values'
~~~
{: .language-bash}

Use `git checkout` to swap between your two branches and view the history (`git log`) and content of the `goostats.sh` file for each branch.
When you are happy with the changes push your new branch to github, using `git push origin`
Checkout your main branch before moving on.

~~~
git checkout main
~~~
{: .language-bash}


You now have two branches in your repository: `main` and `compute-range`.
From here you could continue development on the `compute-range` branch while you or your collaborators are *using* the main branch for their normal work.
Even if you break something in the `compute-range` branch, it will not cause problems for people using the `main` branch.


> ## Merge your branch back into main
> Checkout your `main` branch.
>
> Merge changes from your `compute-range` branch using:
> ~~~
> git merge compute-range
> ~~~
> {: .language-bash}
>
> You will (hopefully) get a message showing the files which were changed during the merge.
> If there are no conflicts then git will just add the commits from the `compute-range` branch to your main branch.
>
> Confirm that you have commits to push using `git status` and then push these to github.
>
> Now that you have all your changes on GitHub you don't need your local feature branch so you can delete it with:
> ~~~
> git branch -D compute-range
> ~~~
> {: .language-bash}
>
> Head to GitHub and check that all your changes have been included on the main branch.
{: .challenge}


### Feature branching workflow
A highly recommended git workflow is the feature branching workflow.
In this workflow, you try to keep your `main` branch as stable as possible, while making your updates and changes in side branches which we call **feautre branches**.
As a feature is being developed changes will often break the functionality of the software so keeping all these changes in a branch separate from `main` will mean that there is always a 'known working' version of the code that people can use.

Consider the case where you are working on a new feature for your code.
You pull the main branch and start developing that feature.
As you are part way through you find a bug that needs to be fixed in the code.
You now either have to make that bug fix part of the feature development, meaning that you cant push it back to the main repository until your development is complete, or you have to discard your development in order to fix the bug, before retuning.

Now consider how this would work if you used a feature branching workflow.
You make a new branch from `main` for `compute-range` and start working on it.
You notice a bug in the main code so you create a new branch from `main` called `bugfix-1`.
You fix the bug in `bugfix-1` and then merge it back to `main` and then also to `compute-range`.
You can now return to developing on `compute-range` without having to backtrack, and you still have the bugfix incorporated.

![FeatureBranching](https://wac-cdn.atlassian.com/dam/jcr:09308632-38a3-4637-bba2-af2110629d56/07.svg?cdnVersion=745)

Another advantage to the feature branching workflow is that by having the branches exist in the central repo, you can have multiple people working on (testing/reviewing) them at the same time.
The feature branching workflow includes a new operation that we haven't discussed yet: a pull (or merge) request.
A pull request (or PR) is initiated on the central repository (eg, GitHub), and is a request to pull changes from one branch into another.
The idea is that developer A will make a bunch of changes in their feature branch, and then when they are happy with the changes, they will create a PR to merge these into another branch (usually main).
Good practice is to then have a different developer act as a reviewer for these changes.
Developer B will look at what the feature branch is trying to address, what has changed, and check that tests are still passing, new tests have been created, and documentation has been created/updated.
Once the reviewer is happy they approve the PR and the feature branch is merged.
For solo developers the PR is not always required, but is still sometimes used as it can cause automated testing to be run.
Even in small teams, it can be very beneficial to require all changes to the main branch to be done via pull requests from a feature branch, with some code review and discussion before the PR is accepted.
Again, Atlassian have a [more detailed description](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow) of the feature branching workflow.
