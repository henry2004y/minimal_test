---
title: Git Version Control
tags:
  - computer
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2024-04-04
---

I haven't considered myself a programmer until very recent years. As a proof of that, 2017/08/28 is the first day I use Git.
My experience of using a version control system started with CVS, a centralized management tool.
Before mid 2019, our research group were using CVS for version control.
The adopted concept is that there is one and only one branch on the server, and everyone commits to that.
This is good for maintainence within a few people.
Back in 2005, Linus Torvalds, the creator of Linux, created Git in a very special scanerio: the Linux kernel community could no longer use their revision control system BitKeeper and no other Source Control Management (SCMs) met their needs for a distributed system.
Linus took the challenge into his own hands and disappeared over the weekend to emerge the following week with Git.
In the same year, he made a [interval talk](https://www.youtube.com/watch?v=4XpnKHJAok8&t=2884s) at Google to explain his motivation.

Time proves Linus was right. Nowadays, Git the go-to option for revision control.
Quoted from his own word:
> I think that many others had been frustrated by all the same issues that made me hate SCM’s, and while there have been many projects that tried to fix one or two small corner cases that drove people wild, there really hadn’t been anything like git that really ended up taking on the big problems head on. Even when people don’t realize how important that “distributed” part was (and a lot of people were fighting it), once they figure out that it allows those easy and reliable backups, and allows people to make their own private test repositories without having to worry about the politics of having write access to some central repository, they’ll never go back.

I happen to know somebody who is strongly against Git but have to give up the fight and use it instead.
However, if your workflow philosophy does not change, you won't touch the true power of git.
So, what is wrong with CVS? By far the cleanest explanation I find is from Zack Brown:
> There were many complaints about CVS though. One was that it tracked changes on a per-file basis and didn't recognize a larger patch as a single revision, which made it hard to interpret the past contributions of other developers. There also were some hard-to-fix bugs, like race conditions when two conflicting patches were submitted at the same time.

CVS is really terrible when I want to reorganize the whole directory or the Internet connection is off.
Besides, I have some other opinions. The key concept behind Git is its distributed storage. I'll make a bet that if you are a truly open-source person, you will love this idea.

So now when you go back and revisit this story of Git, you can feel the importance of vision for the future. New things are born because we are tired of the old ones. The popularity of Git and its workflow has a reason.

By the way, for a serious project, **NEVER push directly to the master branch**. There are all kinds of tool to do automated testing before you are confident about the changes.

With all that being said, git is still not perfect. Recently I was confused about the usage of `git rebase`, so I did a search on the topic. Both `merge` and `rebase` are used for merging branches, but the differences are summarized [here](https://www.perforce.com/blog/vcs/git-rebase-vs-merge-which-better). Based on my current understanding, `rebase` is better than merge for local unpublished commits; `rebase` may create a disaster in conflicts if being used on commits that are already pushed/shared on a remote repository![^1]

[^1]: Practically Gabor's forced usage of `rebase` only works if you have a single branch to work on. Do not make it the default synchronizing behavior!

## Adding

The first principle of commits is one thing at a time. For instance, if you have two independent changes in a file, the basic `git -add file` will list both changes togther. A more advanced way is `git add -p file`: this allows you to go through all the changes and decide whether they belong to the same topic!

<iframe width="560" height="315" src="https://www.youtube.com/embed/Uszj_k0DGsg?start=90" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Renaming

Often git may get confused about renaming files. Check [this StackOverflow discussion](https://stackoverflow.com/questions/433111/how-to-make-git-mark-a-deleted-and-a-new-file-as-a-file-move) and the trick of using `git log --follow` in [Follow the History of renamed files](https://kgrz.io/use-git-log-follow-for-file-history.html).

The important thing to know is that internally git does not have the concept of renaming: it only knows deleting and adding.
A more thorough explanation can be found [here](https://vjeko.com/2020/11/24/understanding-renaming-moving-files-with-git/).
In most common cases, when you try to reorganizing your code, consider separate the renaming stage and content modification stage.

## Squashing

Generally it is better to squeeze small commits before merging, especially in a PR. Take a look at this post [Combining multiple commits before pushing](https://stackoverflow.com/questions/6934752/combining-multiple-commits-before-pushing-in-git).

## Finding regression

`git bisect` is a great tool for finding regression. Check [how to use git bisect](https://stackoverflow.com/questions/4713088/how-to-use-git-bisect).

## Blaming

`git blame` shows the modification history of a file. There are now built-in GUI supports on GitHub and GitLab.

## Deleting a branch

[How to delete a branch](https://stackoverflow.com/questions/2003505/how-do-i-delete-a-git-branch-locally-and-remotely)

## Resolving conflicts

### When does conflicts may occur

* `git merge`
* `git rebase`
* `git pull`
* `git cherry-pick`
* `git stash apply`

### How to undo a conflict and start over

You can always undo and start fresh!

```shell
git merge --abort
```

```shell
git rebase --abort
```

### How to solve a conflict

Simply clean up the file!

```shell
git mergetool
```

## Removing from history

Removing a file completely from Git history requires extra care, which is described in [Removing sensitive data from a repository](https://docs.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository).

Remember to tell your collaborators working on their local branches to rebase from origin/master by `git pull --rebase` instead of `git pull`. Otherwise one merge commit could reintroduce some or all of the tainted history that you just went to the trouble of purging!

Sometimes you may want to remove file from the repository but keep it locally. This can be achieved by

```shell
git rm --cached -r somedir
```

Will stage the deletion of the directory, but doesn't touch anything on disk. This works also for a file, like:

```shell
git rm --cached somefile.ext
```

Afterwards you may want to add `somedir/` or `somefile.txt` to your `.gitignore` file so that git doesn't try to add it back.


## Workflow within a group

It is easy to use git if you are the only person who commits.
It becomes tricky if you are working with a group of people.

I originally made some mistakes in the git workflow when I started working in a new group on GitHub.
The team chooses to merge every new feature and bug fixes into the dev branch instead of the master branch.
So everytime you fork the repository, commit some changes, and then submit a pull request to the main dev branch.
At first I was not used to working on new feature branches.
I followed my old CVS way of dumping everything I did into my forked branch, and gradually it became a huge change.
This is not good for reviewing.
What made it worse was that others were submitting pull requests also. Once their commits are merged into the latest dev branch, my huge branch will have many commits ahead and also several commits behind, which is likely to lead to conflicts that must be resolved manually.
Here is a good chance to emphasize the usage of `rebase`: if any commits have been made to the upstream master branch, you should rebase your development branch so that merging it will be a simple fast-forward that won't require any conflict resolution work.
It is also useful in the situation where you are developing two new feature branches A and B, and B is depending on A.
You submit your PR of A while working on B.
Later you receive feedbacks on A, making changes, and rebase B on top of that.
If you don't use `rebase`, you have to resolve the conflicts everytime you modify A, and eventually at the time you submit PR of B, it will become a huge mess.
Similar situation is being described [here](https://stackoverflow.com/questions/56875810/new-pull-request-when-one-is-already-opened).
Learn from yours and others' mistakes!

Here is the suggested workflow.
"Working off a branch" usually means you

1. clone a repository, e.g. `git clone http://repository`
2. check out the branch you're interested in `git checkout awesome-branchname`,
3. and create a new branch based of that `git checkout -b new-even-more-awesome-branch-name`

Beyond that, you also need to

1. [configure the remote for your fork](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/configuring-a-remote-for-a-fork)
2. [sync your fork](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/syncing-a-fork)
3. [rebase your development branch](https://gist.github.com/Chaser324/ce0505fbed06b947d962#cleaning-up-your-work)

Additionally, it would be better if you squeeze your small commits into large ones with concise messages, and push to remote like this:

```shell
git checkout my_branch
git reset --soft HEAD~4
git commit
git push --force origin my_branch
```

Sometimes `rebase` will become a tedious option if you jump between branches. In this case, `cherry-pick` may be an easy alternative. Check out
[How to Cherry Pick Commits]https://stackoverflow.com/questions/1670970/how-to-cherry-pick-multiple-commits) for more.

As with many peer review projects, pull requests take a long time in the queue waiting to be merged.
Handle the situation wisely to save your time and effort!

Here is an excellent [guidance for what to do on GitHub](https://gist.github.com/Chaser324/ce0505fbed06b947d962).

## GitHub

In Fall 2021, GitHub disabled direct command line push through HTTPS even if you are the owner. They introduced a new authentication key which only shows up once and has a limited available time. Now the recommended way to connect to GitHub is through SSH. Follow the steps in [Q&A](https://stackoverflow.com/questions/60757334/git-push-from-vs-code-no-anonymous-write-access-authentication-failed) to setup the SSH connection to GitHub.

## Resources

There is a summary of [Git best practices](https://deepsource.io/blog/git-best-practices/).
Even after I scanned through it, I still made all kinds of mistakes. That's fine. This is how you improve.

Never or less, happy coding!
