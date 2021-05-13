---
description: 'https://git-scm.com/'
---

# Git

## Notes

* When you run the `git init` command, Git creates a `.git` directory in the directory from where the command was launched. Everything Git needs to work is in there.
* When you run the `git add` command, Git writes files to the _staging area_ of the repository. When you run `git commit`, Git creates a commit object \(with an ID\), and it applies references \(= labels\) to that commit \(`HEAD` and the branch you are on\).
* When you run the `git branch` command, Git just created a new reference with the name of the newly created branch, and applies it to the `HEAD` commit.
* When you run the `git checkout` command, Git switches the branch it's going to advance when you commit.
* An ID in Git is a checksum of the content of the commit, the author, the date, the log message, and the previous commit ID. Every ID is unique, so every commit is unique \(as changing even slightly just one of the variables above will generate a completely different checksum\).
* When you run the `git merge <branch>` command, two situations are possible:
  * Git recognises the current branch is an ancestor of the branch to be merged: in this case, Git will perform the merge by **fast-forwarding**, i.e. it will just update references;
  * Git recognises the current branch is **not** an ancestor of the branch to be merged: in this case, Git will create a new commit \(with two parents\), and will move the `master` and the `HEAD` references \(but **not** the branch one.
* Thanks to the fact that every ID is unique, every commit is unique, commits do not change, and IDs depend on previous commits, Git is able to retrieve the full history of a commit just given its ID. This is very useful when you run the `git push` command, as it will send to origin only the minimum set of commits to make the two repositories the same.
* When you run `git tag`, Git will create a new reference and attach it to a commit. The main difference of a tag is that it **never moves** after being created.
* The **reflog** is a summary of the latest commits Git has interacted with. It can be useful in rescue situations when some of the commits have been done from a `detached HEAD` situation, meaning there was no branch associated with them.

## Useful commands

### Add a new remote repository

`git remote add <remote_name> <remote_url>`

### Create a fixup commit

`git commit --fixup <hash>`

### Rename current branch

`git branch -m <new_name>`

## Resources

### Articles

* [A look under the hood: how branches work in Git](https://stackoverflow.blog/2021/04/05/a-look-under-the-hood-how-branches-work-in-git/) - Tobias Günther
* [Commits are snapshots, not diffs](https://github.blog/2020-12-17-commits-are-snapshots-not-diffs/) - Derrick Stolee
* [git - the simple guide - no deep shit!](https://rogerdudler.github.io/git-guide/) - Roger Dudler
* [Most common git screwups/questions and solutions](https://41j.com/blog/2015/02/common-git-screwupsquestions-solutions/)

### Books

* [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/) - John Wiegley
* [Pro Git](https://git-scm.com/book/en/v2) - Scott Chacon, Ben Straub

### Videos

* [Git For Ages 4 And Up](https://www.youtube.com/watch?v=1ffBJ4sVUb4) - Michael Schwern

### Websites

* [Git Exercises](https://gitexercises.fracz.com/)
* [Learn Git Branching](https://learngitbranching.js.org/)

