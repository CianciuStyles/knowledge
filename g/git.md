---
description: https://git-scm.com/
---

# Git

## Notes

* When you run the `git init` command, Git creates a `.git` directory in the directory from where the command was launched. Everything Git needs to work is in there.
* When you run the `git add` command, Git writes files to the _staging area_ of the repository. When you run `git commit`, Git creates a commit object (with an ID), and it applies references (= labels) to that commit (`HEAD` and the branch you are on).
* When you run the `git branch` command, Git just created a new reference with the name of the newly created branch, and applies it to the `HEAD` commit.
* When you run the `git checkout` command, Git switches the branch it's going to advance when you commit.
* An ID in Git is a checksum of the content of the commit, the author, the date, the log message, and the previous commit ID. Every ID is unique, so every commit is unique (as changing even slightly just one of the variables above will generate a completely different checksum).
* When you run the `git merge <branch>` command, two situations are possible:
  * Git recognises the current branch is an ancestor of the branch to be merged: in this case, Git will perform the merge by **fast-forwarding**, i.e. it will just update references;
  * Git recognises the current branch is **not** an ancestor of the branch to be merged: in this case, Git will create a new commit (with two parents), and will move the `master` and the `HEAD` references (but **not** the branch one).
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

### Alternatives

* [Fossil](https://www.fossil-scm.org/home/doc/trunk/www/index.wiki)
* [Pijul](https://pijul.org/)
* [Sapling](https://sapling-scm.com/) - Meta

### Articles

* [A look under the hood: how branches work in Git](https://stackoverflow.blog/2021/04/05/a-look-under-the-hood-how-branches-work-in-git/) - Tobias Günther
* [Better Git Conflicts with zdiff3](https://ductile.systems/zdiff3/) - Michael Gattozzi
* [Commits are snapshots, not diffs](https://github.blog/2020-12-17-commits-are-snapshots-not-diffs/) - Derrick Stolee
* [Confusing git terminology](https://jvns.ca/blog/2023/11/01/confusing-git-terminology/) - Julia Evans
* [Demystifying git submodules](https://www.cyberdemon.org/2024/03/20/submodules.html) - Dmitry Mazin
* [git - the simple guide - no deep shit!](https://rogerdudler.github.io/git-guide/) - Roger Dudler
* [git branches: intuition & reality](https://jvns.ca/blog/2023/11/23/branches-intuition-reality/) - Julia Evans
* Git's database internals - Derrick Stolee
  * [Part I: packed object store](https://github.blog/2022-08-29-gits-database-internals-i-packed-object-store/)
  * [Part II: commit history queries](https://github.blog/2022-08-30-gits-database-internals-ii-commit-history-queries/)
  * [Part III: file history queries](https://github.blog/2022-08-31-gits-database-internals-iii-file-history-queries/)
  * [Part IV: distributed synchronization](https://github.blog/2022-09-01-gits-database-internals-iv-distributed-synchronization/)
  * [Part V: scalability](https://github.blog/2022-09-02-gits-database-internals-v-scalability/)
* [Git Tips and Tricks](https://blog.gitbutler.com/git-tips-and-tricks/) - Scott Chacon
  * [Git Tips 1: Oldies but Goodies](https://blog.gitbutler.com/git-tips-1-theres-a-git-config-for-that/)
  * [Git Tips 2: New Stuff in Git](https://blog.gitbutler.com/git-tips-2-new-stuff-in-git/)
  * [Git Tips 3: Really Large Repositories](https://blog.gitbutler.com/git-tips-3-really-large-repositories/)
* [How HEAD works in git](https://jvns.ca/blog/2024/03/08/how-head-works-in-git/) - Julia Evans
* [In a git repository, where do your files live?](https://jvns.ca/blog/2023/09/14/in-a-git-repository--where-do-your-files-live-/) - Julia Evans
* [Inside .git ](https://jvns.ca/blog/2024/01/26/inside-git/)- Julia Evans
* [Modern Git Commands and Features You Should Be Using](https://martinheinz.dev/blog/109) - Martin Heinz
* [Most common git screwups/questions and solutions](https://41j.com/blog/2015/02/common-git-screwupsquestions-solutions/)
* [Picturing Git: Conceptions and Misconceptions](https://www.biteinteractive.com/picturing-git-conceptions-and-misconceptions/) - Matt Neuburg
* [Popular git config options](https://jvns.ca/blog/2024/02/16/popular-git-config-options/) - Julia Evans
* [The "current branch" in git](https://jvns.ca/blog/2024/03/22/the-current-branch-in-git/) - Julia Evans
* [What is in that .git directory?](https://blog.meain.io/2023/what-is-in-dot-git/) - Abin Simon
* [When to Use Each of the Git Diff Algorithms](https://luppeng.wordpress.com/2020/10/10/when-to-use-each-of-the-git-diff-algorithms/) - Lup Peng

### Books

* [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/) - John Wiegley
* [Pro Git](https://git-scm.com/book/en/v2) - Scott Chacon, Ben Straub

### Images

<figure><img src="../.gitbook/assets/Git Commands Cheatsheet.jpg" alt=""><figcaption></figcaption></figure>

### Tools

* [lazygit](https://www.youtube.com/watch?v=4DWKf5RqU-s)
  * [15 Lazygit Features In Under 15 Minutes](https://www.youtube.com/watch?v=CPLdltN7wgE)

### Videos

* [Git For Ages 4 And Up](https://www.youtube.com/watch?v=1ffBJ4sVUb4) - Michael Schwern
* [So You Think You Know Git](https://www.youtube.com/watch?v=aolI_Rz0ZqY) - Scott Chacon
* [So You Think You Know Git Part 2 ](https://www.youtube.com/watch?v=Md44rcw13k4)- Scott Chacon

### Websites

* [Git Exercises](https://gitexercises.fracz.com/)
* [Git Explorer](https://gitexplorer.com/)
* [Learn Git Branching](https://learngitbranching.js.org/)
* [Oh My Git!](https://ohmygit.org/)
