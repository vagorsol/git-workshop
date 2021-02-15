# CS71 Lab 8: Git Workshop
<span style="color:#555555">
In which, mysteries long hidden are revealed...
</span>

- - -

The goals for this lab assignment are:

* Understanding version control concepts
* Understanding how Git stores version control information
* Learning how to read Git logs and diffs
* Learning how to recover old versions of files and switch between versions

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
* [Swarthmore CS Git Getting Started Guide](https://www.cs.swarthmore.edu/~newhall/unixhelp/git.php)

- - - - - - - 

## Creating a new repository


To start, we will create a new repository. There are two ways you can do this:

1. Create a new repository on github and then clone it to your local machine
2. Create a repository locally and then upload it to github

Let's use method #1 to create our repository:

* From github, go to your repositories and click on the button 'New'. 
   * Name your repository "git-practice". 
   * Check the button "Initialize this repository with a readme"
   * Click "Create repository"

* On your local machine, use `git clone` to download the repository from github. The 'Clone or download' button 
will provide a copy-able link which you can use to clone. The command should look like:

```
git clone git@github.swarthmore.edu:<USERNAME>/git-practice.git
```

* Add your professor as a collaborator. Go to 'Settings' -> 'Collaborators' and add anormoy1. 

The following sections contain questions for you to fill out. Put your answers in README.md.

- - - - - - - 

## Analyzing an existing Git repository

Git represents histories as a [directed acyclic graph
(DAG)](https://en.wikipedia.org/wiki/Version_control#Graph_structure). Each
node corresponds to a `commit` made to the repository. 

Let's look at the example from [MIT's 6.005 version control lecture](https://ocw.mit.edu/ans7870/6/6.005/s16/classes/05-version-control/#copy_an_object_graph_with_git_clone).

```
git clone https://github.com/mit6005/sp16-ex05-hello-git.git hello-git
```

First, let's configure Git so that logs look pretty when we print them:

```
git config --global color.branch auto
git config --global color.diff auto
git config --global color.interactive auto
git config --global color.status auto
git config --global color.grep auto
```

And next, let's create an alias `git lol` for showing the log.
```
git config --global alias.lol "log --graph --oneline --decorate --color --all"
```

Go into your `hello-git` directory and try running `git lol`. You should see
something like the following (but in color!):

```
$ **git lol**
* b0b54b3 (HEAD -> master, origin/master, origin/HEAD) Greeting in Java
*   3e62e60 Merge
|\  
| * 6400936 Greeting in Scheme
* | 82e049e Greeting in Ruby
|/  
* 1255f4e Change the greeting
* 41c4b8f Initial commit
```

Each node represents a version, e.g. snapshot of the entire project. It follows that 
each node corresponds to a `commit`. Every commit has a unique ID and a pointer to 
its parent commit. For example, 

* The commit with ID b0b54b3 has parent 3e62e60
* The commit with ID 3e62e60 has two parents: 6400936 and 82e049e
* The commits with IDs 6400936 and 82e049e both have the same parent: 1255f4e
* The commit 41c4b8f has no parent because it's the root.

![](https://ocw.mit.edu/ans7870/6/6.005/s16/classes/05-version-control/figures/hello-git-history.png)

We can also view the history using tools like `gitg` (try it!)

![](GitHistory.png)

We are currently on a branch called *master*. It is a local repository. The
remote repository is located at *origin/master*.  When we `git clone`, we
download a copy of the repository's history graph. Git uses **origin/master** like a
bookmark to remember where we cloned from. 

* When we `git push`, we upload our local commits to the remote repository
* When we `git pull`, we download remote commits to our local repository


Git also needs to store information about the contents of all its files. Git does this using **tree nodes**. Tree 
nodes store diffs between versions as well as human-friendly log messages. If we visualize the tree nodes, our 
graph now looks like:

<!--For example, let's look at the log:
```
hello-git$ **git log**
commit b0b54b3780c95ff74d94f374572078a496500a83 (HEAD -> master, origin/master, origin/HEAD)
Author: Max Goldman <maxg@mit.edu>
Date:   Mon Sep 14 16:02:40 2015 -0400

    Greeting in Java

commit 3e62e60a7b4a0c262cd8eb4308ac3e5a1e94d839
Merge: 82e049e 6400936
Author: Ben Bitdiddle <ben.bitdiddle@example.com>
Date:   Mon Sep 14 15:08:40 2015 -0400

    Merge

commit 82e049e248c63289b8a935ce71b130a74dc04152
Author: Ben Bitdiddle <ben.bitdiddle@example.com>
Date:   Mon Sep 14 15:06:10 2015 -0400

    Greeting in Ruby

commit 64009369c5ab93492931ad07962ee81bda921ded
Author: Alyssa P. Hacker <alyssa.p.hacker@example.com>
Date:   Mon Sep 14 15:03:30 2015 -0400

    Greeting in Scheme

commit 1255f4e4a5836501c022deb337fda3f8800b02e4
Author: Max Goldman <maxg@mit.edu>
Date:   Mon Sep 14 14:58:40 2015 -0400

    Change the greeting

commit 41c4b8f6c855028294c609885219987cb8040158
Author: Max Goldman <maxg@mit.edu>
Date:   Mon Sep 14 14:56:00 2015 -0400

    Initial commit
```
-->


![](https://ocw.mit.edu/ans7870/6/6.005/s16/classes/05-version-control/figures/hello-git-history-trees.png)

The last important book-keeping Git performs involves the **staging area** (aka **index** inside `.git`). This 
is where Git stores your file state with you do a `git add`. 

The history, tree, and staging area are all pieces of the **object graph** that Git uses to manage repositories. 
So far, we've talked about what information is stored in the object graph, but not *where* it stores this information. The 
answer is that Git stores everything inside a hidden directory named `.git`. 

```
hello-git$ **ls -la**
total 40
drwxrwxr-x 3 alinen alinen 4096 Mar 31 15:25 .
drwxrwxr-x 3 alinen alinen 4096 Mar 31 15:26 ..
-rw-rw-r-- 1 alinen alinen  223 Mar 31 15:25 .classpath
drwxrwxr-x 8 alinen alinen 4096 Mar 31 15:25 .git  <------ location of all .git state
-rw-rw-r-- 1 alinen alinen    5 Mar 31 15:25 .gitignore
-rw-rw-r-- 1 alinen alinen  211 Mar 31 15:25 Hello.java
-rw-rw-r-- 1 alinen alinen   31 Mar 31 15:25 hello.rb
-rw-rw-r-- 1 alinen alinen   36 Mar 31 15:25 hello.scm
-rw-rw-r-- 1 alinen alinen   30 Mar 31 15:25 hello.txt
-rw-rw-r-- 1 alinen alinen  368 Mar 31 15:25 .project
```

### Exercise 1: A new commit

Edit `hello.txt` and commit your changes. 

**1a.** Use the output of `git lol` to find the ID of your commit<br>
**1b.** Use `git log` to find out the time stamp for your commit<br>
**1c.** Where are the pointers for Head in both the local and remote repositories?<br>

The output of `git status` says:

```
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

**1d.** What does this message mean: "Your branch is ahead of 'origin/master' by 1 commit."? <br>
**1e.** Why does Git report: "nothing to commit, working tree clean"?<br>

### Exercise 2: Merging

We can see from the graph that Alyssa and Ben both made simultaneous commits. Read the description 
[here](https://ocw.mit.edu/ans7870/6/6.005/s16/classes/05-version-control/#merging) to see what happened.

**2a.** Why is Ben unable to push his commit without merging?<br>
**2b.** If Ben had pushed his code before Alyssa, how would the history graph change?<br>

### Exercise 3: Looking at files
 
Use the `git show` command to see the contents of any commit. 

```
$ git show 1255f4e
commit 1255f4e4a5836501c022deb337fda3f8800b02e4
Author: Max Goldman <maxg@mit.edu>
Date:   Mon Sep 14 14:58:40 2015 -0400

    Change the greeting

diff --git a/hello.txt b/hello.txt
index c1106ab..3462165 100644
--- a/hello.txt
+++ b/hello.txt
@@ -1 +1 @@
-Hello, version control!
+Hello again, version control!
```

Note that Git only shows the diffs by default. If we want to see the full commit, add a `:` like so

```
$ git show 3e62e60:
tree 3e62e60:

hello.rb
hello.scm
hello.txt
```

And to see the state of an entire file

```
$ git show b0b54b3:hello.txt
Hello again, version control!
```

**3a.** Use git show to see the how to file `hello.txt` changed each commit. Copy and paste the commands in your README.md<br>

### Exercise 4: Reverting to a previous version

Suppose we wish to undo our changes and revert back to the state of the remote
repository of our last pull (or clone). DANGER ZONE: THIS WILL CLOBBER YOUR LOCAL COMMITS (LOSES WORK)! 

```
$ **git reset --hard origin**
HEAD is now at b0b54b3 Greeting in Java
$ **git lol**
* b0b54b3 (HEAD -> master, origin/master, origin/HEAD) Greeting in Java
*   3e62e60 Merge
|\  
| * 6400936 Greeting in Scheme
* | 82e049e Greeting in Ruby
|/  
* 1255f4e Change the greeting
* 41c4b8f Initial commit
```

Suppose we wish to revert a previous version *without* losing all our changes. 
We can use `git checkout <ID>`. Try the following:

```
$ **git checkout 1255f4e**
Note: checking out '1255f4e'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 1255f4e Change the greeting
```

**4a** What is the output of `git lol`<br>
**4b** The warning message says that we're not allowed to persistent changes to this commit, unless we create a new branch. Why might this be? If we make changes to this version, what would happen to the commits at 82e049e and 6400936? <br>
**4c** If we call `git checkout master`, what happens? Use `git lol`<br>

- - - - 

**Submission** Don't forget to checkin and push your answers!

- - - - 

<div id="footer" style="font-size: 8pt; color: rgb(119, 119, 119);text-align:right;">
</div>
