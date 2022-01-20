# Git Workshop

- - -

The goals for this lab assignment are:

* Understanding source control concepts
* Understanding how to add files and handle merges 

- - - - - - - 

## Tl;DR

Use git to backup your changes early and often. To save changes to your repository do:

```
$ git add .
$ git commit -m "helpful message"
$ git push
```
 
To get changes from the remote repository do:

```
$ git pull
```
 
- - - - - - - 

## Introduction

Git is an example of a [version control system](https://en.wikipedia.org/wiki/Version_control). Other systems are 
SVN, CVS, and RCS. These systems are important because they allow us to 

* track and monitor how our code changes over time
* revert the code to a previous state
* compare versions
* merge versions
* keep track of code releases (via tags or branches)
* work in parallel on the same codebase across teams or across different computers

Github is a hosting service for git-based repositories. Github gives us an easy way to backup our repositoryies on 
a server so they can be shared across machines or with otehr people. Github also provides a lot of helpful project 
documentation and management tools.

- - - - - - - 

## Version control

A version control system maintains a *repository* that stores how a set of files have changed over time. Repositories 
can be local or remote. 

Terminology:

* **Repository**: local or remote store of the versions of our files
* **Working copy**: a local copy of the repository that we can edit
* **Version** or **Revision**: a record of the contents of our files at a certain time
* **Change** or **Diff**: the difference between two versions
* **Head**: A pointer to our current version
* **Checkout**: The process of obtaining files from the repository. After checking out files, you can make changes to them.
* **Check in** or **Commit**: The process of replacing the files in a repository with new versions. Once a file is checked in, any user of the repository has access to the changes. 
* **Branches**: A repository can keep track of multiple versions of the same set of files. Each version is on its own **branch**. The main branch is often called the **trunk** or in Git, **master**. When changes from one branch are incorporated into another, we call it a **merge**. 

A major job of any version control system is maintain the history of edits to the files in its repository. Some 
systems keep track of changes on a *per file* basis. However, this can become problematic when files are moved, renamed, 
or deleted. For this reason, Git keeps track of changes over the *whole repository*, called a **snapshot**. Snapshots 
record edits as well as additions and removals to the repository.

Additional Readings:

* [MIT Lecture: Version Control](https://ocw.mit.edu/ans7870/6/6.005/s16/classes/05-version-control/)
* [Pro Git](http://git-scm.com/book) documents everything you might want to know. Reading [Pro Git: Git Basics](http://git-scm.com/book/en/v2/Getting-Started-Git-Basics) is a good place to start.
* [Git command reference](http://git-scm.com/docs)

- - - - - - - 

## Best Practices

* Write descriptive commit messages, especially for non-trivial changes.
* Commit early and often. There is no downside to commiting your work. You can always get back an old version of a file. 
* Push changes to ensure that your work is backed-up.

- - - - - - - 

## Forking a repository

To start, create a fork of this repository. This will copy it to your own account on Github.

On your local machine, use `git clone` to download the repository from github. The 'Clone or download' button 
will provide a copy-able link which you can use to clone. The command should look like:

```
git clone git@github.com:<USERNAME>/git-workshop.git
```

Now, there are two copies of the repository. One is on the server, hosted at Github. The other is on your 
local machine!

>NOTE: `.md` stands for markdown. Markdown can be used to generate styled HTML using easy-to-read text files. In fact, this README is written in 
>markdown. You can look at the raw file to see what it looks like. 
>[Click here for a markdown reference](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)

- - - - - - - 

## Exercise 1: Your first commit

A *commit* is a snapshot of your project, saved in git. Git assigns each commit a unique identifier. Under the hood, git represents each
commit as a node in an acylic tree data structure. Edges represent changes between versions. A repository is changed whenever a 
developer adds, removes, or edits a file.

Create a new file `hello.txt`, write a fun message, and save it under `git-workshop`.  

* `git status` shows you the current changes to the repository
* `git add <filenames>` will tag files for committing. This is called **staging** files for commit. You can use file patterns or names. 
  * `git add .` adds every change to the staging area, recursively from the current directory
  * `git add *.cpp` adds every modified cpp file to the staging area, recursively from the current directory
  * `git add hello.txt` adds one file
* `git commit -m "Add fun message"` commits the staged file(s) with the given message. **Best practice is to always specifiy a descriptive commit messages**. These messages will help you navigate your git history latter. 

Below is an example of running the above commands

```
alinen@Xin /cygdrive/c/alinen/cs312/git-workshop
$ ls
LICENSE  README.md  hello.txt

alinen@Xin /cygdrive/c/alinen/cs312/git-workshop
$ cat hello.txt
This is a fun message.

alinen@Xin /cygdrive/c/alinen/cs312/git-workshop
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        hello.txt

nothing added to commit but untracked files present (use "git add" to track)

alinen@Xin /cygdrive/c/alinen/cs312/git-workshop
$ git add hello.txt

alinen@Xin /cygdrive/c/alinen/cs312/git-workshop
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   hello.txt


alinen@Xin /cygdrive/c/alinen/cs312/git-workshop
$ git commit -m "A fun message"
[main 783f061] A fun message
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt

```

We have now saved our changes locally. Use `git log` to see a summary of all the changes made to the repository so far. Notice which commits 
the pointers `HEAD` and `origin/HEAD` point to.

To backup the changes remotely, you need to `push` the changes back to github.

```
alinen@Xin /cygdrive/c/alinen/cs312/git-workshop
$ git push
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 329 bytes | 65.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:BrynMawr-CS312-2021/git-workshop.git
   8ead3cf..783f061  main -> main
```

You can see a log of the changes made to the repository. The log shows the IDs and timestamps of each commit. Below, the 
repository was changed 3 times: an intial commit, adding a README.md file, and adding hello.txt. The commit ID where we added 
`hello.txt` was 783f0610dbf6c0a357afd9f6e4c4fc18ea595004. Notice that the `HEAD` points to the same commit on both the local 
and remote repositories. 

**Your local copy should now match the remote copy on github!**

**NOTE:** You can tell git to ignore changes from certain files by listing them in `.gitignore`. Best practice is to put generated and temporary files into `.gitignore`. For example, the following `.gitignore` file ignores common generated files from vim and macOS.

```
*~
Debug
Release
.DS_Store
```

- - - - - - - 

## Exercise 2: Merges and conflicts

In this section, we will see how git handles changes to a repository that are not made locally. These features aloow multiple people to collaborate on the same project simultaeously.  

1. Edit this Readme on Github.

2. Pull the changes to your local respository. It should look something like the following:

```
alinen@Xin MINGW64 /c/alinen/cs312/git-workshop (main)
$ git pull
Updating 783f061..baa94e5
Fast-forward
 README.md | 178 ++++++++++++++++++++++++++++++++++++++++++++++++++++----------
 1 file changed, 151 insertions(+), 27 deletions(-)
 ```

Now, let's see what happens when you have a conflict.

1. Edit your file, `hello.txt`, on Github

2. Now, edit the file `hello.txt` on your local computer and commit the changes. This changes the file on the local respository. 
Now try to push your changes. You will get this error: 

```
alinen@Xin MINGW64 /c/alinen/cs312/git-workshop (main)
$ git push
To github.com:BrynMawr-CS312-2021/git-workshop.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'git@github.com:BrynMawr-CS312-2021/git-workshop.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

3. Download the remote changes to your local repository. Run `git pull` at the command line. It should look like:

```
alinen@Xin MINGW64 /c/alinen/cs312/git-workshop (main)
$ git pull
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 6 (delta 2), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (6/6), done.
From github.com:BrynMawr-CS312-2021/git-workshop
   baa94e5..c6adb85  main       -> origin/main
Auto-merging hello.txt
CONFLICT (content): Merge conflict in hello.txt
Automatic merge failed; fix conflicts and then commit the result.
```

It doesn't work! Git doesn't know which change to keep. To fix the problem, we must edit the file and then check in the change. 

Merging changes is common. Below are other options for dealing with conflicts:

* `git checkout --theirs`: Use the remote version of the file
* `git checkout --ours`: Use the local version of the file

Sometimes you can't pull because you have *uncommitted* changes that conflict with the remote repository. In this case, either commit the 
changes, or throw them away by running `git checkout .`
