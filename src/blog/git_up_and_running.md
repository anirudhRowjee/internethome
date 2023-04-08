---
title: Git Up and Running (Git 101)
date: 2022-02-02
description: This post is my best attempt to put into words a talk I gave (by the same title) at my University a couple of times. Grab a cup of coffee/tea/water, and let's learn about Git!
collections:
  - tech
tags:
  - git
  - github
  - talks
  - latest
  - featured
  - tech
image: /static/images/git_up_and_running/maggi_components.png
---

This post is my best attempt to put into words a talk I gave (by the same title) at my University a couple of times. Grab a cup of coffee/tea/water, and let's learn about Git!

# Why?

Git is an essential, industry-standard [Version Control System (VCS)](https://en.wikipedia.org/wiki/Version_control) - In essence, Git is how you work on the same thing (usually a document or a bunch of files) with multiple people at the same time, without needing to break your head about it.

Remember when you'd save your work at different points in time by naming things roughly this way?

![](/static/images/git_up_and_running/how_not_to.png)

Fear not, my friend! Gone are the days where you'd have to do this. Pick your jaws off the floor, and let me introduce you to Git, which saves you from having to resort to this... practice :D

# What is Git?

Git is a Distributed (i.e. can run on multiple connected computers) Version Control System (VCS) that helps you

- **track changes** made to your files
- **work on multiple different versions** of your files at once (no, I'm not kidding! You can!)
- **revert to an old version** of your files
- **collaborate with other people** on the same files

For all practical means and purposes, Git itself is a piece of software that was written to help the maintainers of the large software projects (Git was written by [Linus Torvalds ](https://en.wikipedia.org/wiki/Linus_Torvalds) and [Junio Hamano](https://simple.wikipedia.org/wiki/Junio_Hamano), both prominent maintainers of the Linux Kernel) do their work easily.

Isn't it amazing that you can use this tool regardless of the scale of your project?

Let's get learning!

# Let's Talk about Maggi.

I like Maggi Noodles.. I think they're excellent, not just as noodles, but as a "meal platform" of sorts - it's a base on which you can build a lot more dishes, much like bread.

The beauty of Maggi (and most noodles) is that we can either have them plain, or we can customize them by adding our own ingredients.

**_In the quest for the ultimate Maggi Noodle recipe, you begin to keep track of all the recipes you try out._**

To do this, you take the help of a Logbook, and a rough piece of paper.

![](/static/images/git_up_and_running/maggi_components.png)

## Some Basic Rules

Before we embark on the wild quest to find the best maggi recipe known to humankind, we set the following rules in place -

1. **Consider a Recipe to be a Collection of Ingredients and Quantities.**

   This helps us quantify things! A recipe can look like this -

   ```
   NOODLES   10
   MASALA    20
   TOMATOES  03
   ```

2. **Each and Every change you make is either an ADDITION (+), DELETION (-) or a MODIFICATION (~) of said ingredients and their quantities.**

   Let's say I started off with the recipe above. I liked it, but I felt like it could use more spice and one less tomato, so the difference would look like this -

   ```
   + SPICE   10
   - TOMATO  01
   ```

   Applying this transformation on the recipe, the new recipe looks something like this -

   ```
   NOODLES   10
   MASALA    20
   TOMATOES  02
   SPICE     10
   ```

3. **The Logbook keeps tracks of versions of recipes by _keeping track of the changes_.**

   At this point, it's easy to see how to undo a change, simply by inverting the signs! Let's say we want to get back to the previous version. The Difference for this change looks like this -

   ```
   - SPICE   10
   + TOMATO  01
   ```

   Magic! We're back where we started.

   The logbook only <u>keeps track of all your changes</u>.

4. **When you're done fiddling around with a recipe on the rough sheet, you write down the changes in the logbook, recording it as a version.**

   Once we're done experimenting, and we like how a certain change to the recipe tastes, we write it down in the logbook!

   Our logbook looks something like this -

   ```
   # Initial Version
   + NOODLES   10
   + MASALA    20
   + TOMATOES  03

   # version 1
   + SPICE   10
   - TOMATO  01
   ```

Whew! That was a bit. Take a minute to make sure your understand what's going on! You'll thank yourself later, I promise you.

It's time to get cooking!

## What's Going On!?

So, you're making a batch of the latest experimental Maggi. You'd obviously want to know how you're changing your recipe, so you write down all your changes on the rough paper before you decide you like it, and so that you can easily modify it.

At any given point of time during our experimentation process, we want to see what's going on in our recipe book. Here's what this set of actions looks like -

|         Action          |                           What it Does                           |
| :---------------------: | :--------------------------------------------------------------: |
|   Make a new logbook!   | starts an empty logbook to immortalize your tastiest experiments |
| Look at the rough paper |         helps you see what momentary changes you've made         |
|   Look at the Logbook   |                see all the past changes you made                 |

## Eventual Comfort

So you've decided on a set of tasty tweaks you want! Here's the rough procedure you follow to immortalize the latest reels-compliant and SEO-Friendly version of your Maggi -

| Step # |                                      Action                                      |
| :----: | :------------------------------------------------------------------------------: |
|   1    | Confirm that your rough sheet has all the changes you want to add to the logbook |
|   2    |       Copy all these changes to the logbook, and give this recipe a name!        |

## The Situation So Far

After a fun day of experimentation, here's what your logbook may look like.

![](/static/images/git_up_and_running/git-1.png)

I could say more about Tobasco sauce (but I digress).

## A Friend wants in!

Having heard about all the fun you've been having with your Maggi, a friend - Ramesh - Wants in!

You tell Ramesh -

> Hey, Here's the system I'm following! Here's why it works, and here's how you can use it!

So, in the true spirit of Open Source, you decide to give Ramesh your recipe collection and logbook to start his own, so that he can see your history of experimentation, and add his own changes, too!

Since you trust Ramesh to make some nice additions, you decide to frequently incorporate his changes to the recipe into yours.

Together, your recipes shall **RULE THE WORLD!** (and also only incidentally taste very nice)

Now, since your logbook is just paper, you can simply copy it all at once, and have multiple copies exist at the same time!

Let's see how we'd go about making a copy and working on it in parallel -

| Step # |                      Action                       |
| :----: | :-----------------------------------------------: |
|   1    |         Make a Xerox Copy of the Logbook          |
|   2    |    Make Changes on both copies simultaneously     |
|   3    | Combine both these versions to get a new version! |

Solid. Both Ramesh and you can work on the logbook at the same time (in essence) and merge your changes later to reach a new, final version.

A Bird's eye view of this process looks something like this -

![](/static/images/git_up_and_running/git-2.png)

Keep in mind that you can have any copy of the logbook be the starting point of an entirely different copy, so it's possible for us to have an arbitrary configuration of these "branches". Think of all the cool diagrams!

### How does this combination happen?

**Since both versions originate from a single point, there's always the possibility you can combine the changes from both versions!** (remember this.)

Your change can look like this -

```
+ SPICE   10
- TOMATO  1
```

And Ramesh's, like this -

```
+ SALT     11
```

Combined, these changes will work the same way!

```
+ SPICE    10
- TOMATO   1
+ SALT     11
```

**What if you both make try to change the same thing?**

What if you said

```
+ SALT     10
```

And Ramesh Said -

```
- SALT     5
```

Our Logbook doesn't know how to combine these two changes! You'll have to either give it an entirely new value, or you'll need to decide whose version to keep, and whose version to discard.

> Communication is important.

This leaves us in a funny place - _**unless you and Ramesh agree upon what to change and what not to change beforehand**_, this situation is common!

It's formally known as a [merge conflict](https://css-tricks.com/merge-conflicts-what-they-are-and-how-to-deal-with-them/), and can be pretty scary, but fear not! You'll get used to resolving them in no time.

# Congratulations! You know know how Git Works.

🥳️

> Wait, what?

Let's make things simpler - it turns out we were talking about code and version control all along!

Let's map our example to Git.

|               Our Name               | What it's called in Git |
| :----------------------------------: | :---------------------: |
|               Logbook                |       Repository        |
|                Recipe                | Any Collection of Files |
|             Rough Paper              |      Staging Area       |
|  A Specific version of the logbook   |         Commit          |
|    The Xerox Copy of the Logbook     |         Branch          |
| To Combine the copy and your version |          Merge          |

In reality, you can apply more or less the same recipe-and-logbook model to software, code and anything you can represent in files!

So far, using this model, we learnt how to -

- **track changes** made to your ~~recipes~~ files
- **work on multiple different versions** of your ~~recipes~~ files at once
- **revert to an old version** of your ~~recipes~~ files
- **collaborate with other people** on the same ~~recipes~~ files

> Sure, okay, I have something of an understanding - but how do I use it? Isn't Git a Command Line tool?

Fear Not! Let's see how these procedures we learnt for our logbook map to Git.

So, Get out your terminals (Git Bash, or just your regular terminal), and make sure you've got git installed. You can download and install it from [here](https://git-scm.com/downloads) - the default options are almost always good enough.

## What's Going On!?

Here's how you can understand what's going on in your repository, in the same context of the cookbook -

|         Action          | Git Command  |                                       What it Does (in real life)                                       |
| :---------------------: | :----------: | :-----------------------------------------------------------------------------------------------------: |
|   Make a new logbook!   |  `git init`  |                                     creates an empty Git repository                                     |
| Look at the rough paper | `git status` | tells you which branch you're on, which files are being tracked, and which changes have been considered |
|   Look at the Logbook   |  `git log`   |                                    look at all the previous commits                                     |

Let's start off by creating an empty repository. We'll then add just one file to it for now, and we'll make changes in it.

**NOTE: Anything after the `$` is a command.**

```shell
$ git init
Initialized empty Git repository in /home/anirudh/git_101/.git/

$ ls -la
total 20
drwxrwxr-x   3 anirudh anirudh  4096 Feb  2 00:08 .
drwxr-x--- 131 anirudh anirudh 12288 Feb  1 23:26 ..
drwxrwxr-x   7 anirudh anirudh  4096 Feb  2 00:08 .git
```

We see that the folder is entirely blank, save for the `.git` folder. This is where the entire repository lives, and it holds everything git needs to know about your repository. Let's see what's going on -

```shell
$ git status
On branch main

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

`git status` (a very useful command to use as often as you want) is telling us what we know - this is a blank folder. We're on the default branch `main`, and we have no commits yet. So let's do what it says, and add a file.

```shell
$ echo "Hello, world!" > hello.txt
$ cat hello.txt
Hello, world!
```

The first command just creates a file called `hello.txt` and writes "Hello, world!" into it. We then use the program `cat` (might not be available on windows, so use a text editor of your choice!) to verify as much. Let's run `git status` to see if that had any effect -

```shell
$ git status
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        hello.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Now Git **knows** that there's a file (or a potential recipe on the rough sheet), and it hasn't been written to the logbook yet! We'll fix this right now.

## Eventual Comfort

Once you've messed around with your code enough, and you think it's time to save it as a version, you can use these commands!

| Step # |                                      Action                                      |         Git Command         |                What it Does (in real life)                |
| :----: | :------------------------------------------------------------------------------: | :-------------------------: | :-------------------------------------------------------: |
|   1    | Confirm that your rough sheet has all the changes you want to add to the logbook |        `git add -A`         | adds all the files in your repository to the staging area |
|   2    |       Copy all these changes to the logbook, and give this recipe a name!        | `git commit -m "<message>"` | Saves the current version of your repository as a commit  |

Let's start off by running `git log` wherever we left our repository.

```shell
$ git log
fatal: your current branch 'main' does not have any commits yet
```

Spot on! We'll fix this by following the steps above.

```shell
$ git status
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        hello.txt

nothing added to commit but untracked files present (use "git add" to track)
```

The output here's remained the same. Let's now use the `git add` command to add all our files to the staging area.

```shell
$ git add -A
$ git status
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   hello.txt
```

We use the `-A` flag here to specify that we want to track all files, so as to not have to specify files individually (you can do that if you wish to!).

Running `git status` immediately after shows us that Git knows, and is keeping track of, this file. It's now ready to write it into our logbook at any point of time.

Let's do that!

```shell
$ git commit -m "first commit!"
[main (root-commit) 39e97d5] first commit!
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt
```

A little involved here - the `-m "<message here>"` is followed to create a "commit message" for each commit. Think of it as leaving a note explaining your changes to anyone else who sees this.

**DO NOT WORRY if the numbers you see aren't the same - they're what are known as [commit hashes](https://www.mikestreety.co.uk/blog/the-git-commit-hash/) and aren't supposed to be the same _by design_**. Commit hashes are unique to a commit, and can be used to identify individual commits.

So you can now see that Git is, indeed, keeping track of insertions and deletions.

Lastly, let's run `git log` to see what's going on -

```shell
$ git log
commit 39e97d52c2c0c26a1c3a314c040c0f01f476a3bc (HEAD -> main)
Author: Anirudh Rowjee <ani.rowjee@gmail.com>
Date:   Wed Feb 2 00:11:40 2022 +0530

    first commit!
```

Here we can see the full commit hash, as well as the details of the person who made the commit - here's where the commit message comes into play! We can also see that the `HEAD` commit (the latest commit) is on the `main` branch.

**Writing a detailed commit message is always helpful, and is a good practice**.

You can have as many commits as you want!

## A Friend wants in!

Here's how we'll deal with collaboration and multiple versions existing at once -

| Step # |                          Action                           |              Git Command              |                     What it Does (In Real Life)                      |
| :----: | :-------------------------------------------------------: | :-----------------------------------: | :------------------------------------------------------------------: |
|   1    |             Make a Xerox Copy of the Logbook              |             `git branch`              |            creates a new branch from your current branch             |
|   2    | Make changes on both copies of the logbook simultaneously | `git status`, `git add`, `git commit` | add files to the staging area and make a new commit with these files |
|   3    |     Combine both these versions to get a new version!     |              `git merge`              |               merges both the branches into one branch               |

Let's start off by **listing all the branches our repository has**.

```shell
$ git branch --list
* main
```

The `*` tells us which branch we're currently on. Good IDEs, good text editors and good shells will go out of their way to make the current git branch obvious to you.

At the moment, only one, but who said we're done? Let's **make a new branch**.

```shell
$ git branch new_branch
$ git branch --list
* main
  new_branch
```

Let's **switch to the branch we just created**.

```shell
$ git checkout new_branch
Switched to branch 'new_branch'
$ git branch --list
  main
* new_branch
```

_Psst! You can both create and switch to a new branch at the same time by using the `-b` flag with `git checkout` - the above could've been accomplished with just `git checkout -b new_branch`_

Now we make some changes to this file, and add a commit or two.

```shell
$ nano hello.txt

$ cat hello.txt
Hello, world!


This is Anirudh, from the new branch!
```

Once we've made changes to the file, we're ready to commit.

```shell
$ git add -A

$ git status
On branch new_branch
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hello.txt

$ git commit -m "made change on branch"
[new_branch 5128622] made change on branch
 1 file changed, 3 insertions(+)
```

Let's switch to our `main` branch, and take a look at the file.

```shell
$ git checkout main
Switched to branch 'main'

$ cat hello.txt
Hello, world!
```

No change! We now have two versions of our file existing at the same time on two branches.

```shell
$ git checkout new_branch
Switched to branch 'new_branch'

$ cat hello.txt
Hello, world!

This is me from a new branch. HI!
```

Quickly checking `git log` tells us the story of two branches -

```shell
$ git log
commit 51286222c4e75db4ffe04f9284d4af19f348a56f (HEAD -> new_branch)
Author: Anirudh Rowjee <ani.rowjee@gmail.com>
Date:   Wed Feb 2 00:16:12 2022 +0530

    made change on branch

commit 39e97d52c2c0c26a1c3a314c040c0f01f476a3bc (main)
Author: Anirudh Rowjee <ani.rowjee@gmail.com>
Date:   Wed Feb 2 00:11:40 2022 +0530

    first commit!
```

`HEAD` points to the latest commit in the repository. For the `new_branch` branch, it's the one where we made a change!

```shell
$ git checkout main
Switched to branch 'main'

$ git log
commit 39e97d52c2c0c26a1c3a314c040c0f01f476a3bc (HEAD -> main)
Author: Anirudh Rowjee <ani.rowjee@gmail.com>
Date:   Wed Feb 2 00:11:40 2022 +0530

    first commit!
```

Since `main` doesn't know about the changes made in `new_branch`, `HEAD` points to a different, earlier commit.

How all this works under the hood is the combination of years of experience and some frankly amazing engineering, which is most definitely out of the scope of this article. Maybe Another Day!

Now, Let's **merge our two branches** to see what happens to the file!

```shell
$ git merge new_branch
Updating 39e97d5..5128622
Fast-forward
 hello.txt | 3 +++
 1 file changed, 3 insertions(+)
```

WOOHOO! Just a quick check confirms that the changes from our branch, have, indeed, been updated into the main branch -

```shell
$ cat hello.txt
Hello, world!


This is Anirudh, from the new branch!
```

Taking a look at `git log` confirms as much.

```shell
$ git log
commit 51286222c4e75db4ffe04f9284d4af19f348a56f (HEAD -> main, new_branch)
Author: Anirudh Rowjee <ani.rowjee@gmail.com>
Date:   Wed Feb 2 00:16:12 2022 +0530

    made change on branch

commit 39e97d52c2c0c26a1c3a314c040c0f01f476a3bc
Author: Anirudh Rowjee <ani.rowjee@gmail.com>
Date:   Wed Feb 2 00:11:40 2022 +0530

    first commit!
```

As we can see, `HEAD` points to both our branches.

If we're on the branch we want to merge into, we simply need to use this command to merge changes from the branch to the main branch.

```shell
$ git merge <branch_name>
```

This about sums it up! You're now ready to use Git, and you know enough to debug any issues you might run into with this, or at least use google to debug better!

## The Git Cheat Sheet by Anirudh Rowjee

Feel free to take a screenshot of this, and keep it around as a handy reference :D

|                                      Action                                      |           Git Command           |                                          What it Does (in Git)                                          |
| :------------------------------------------------------------------------------: | :-----------------------------: | :-----------------------------------------------------------------------------------------------------: |
|                               Make a new logbook!                                |           `git init`            |                                     creates an empty Git repository                                     |
|                             Look at the rough paper                              |          `git status`           | tells you which branch you're on, which files are being tracked, and which changes have been considered |
|                               Look at the Logbook                                |            `git log`            |                                    look at all the previous commits                                     |
| Confirm that your rough sheet has all the changes you want to add to the logbook |          `git add -A`           |                        adds all the files in your repository to the staging area                        |
|       Copy all these changes to the logbook, and give this recipe a name!        |   `git commit -m "<message>"`   |                        Saves the current version of your repository as a commit                         |
|                         Make a Xerox Copy of the Logbook                         |   `git branch <branch name>`    |                              creates a new branch from your current branch                              |
|                  Look at a different Xerox Copy of the logbook                   |  `git checkout <branch name>`   |                            switches to a different branch of the repository                             |
|               Make a new copy and look at the copy of the logbook                | `git checkout -b <branch name>` |                    creates a new branch from your current branch and switches to it                     |
|                Combine both these versions to get a new version!                 |    `git merge <branch name>`    |                                merges both the branches into one branch                                 |

# What now?

You've made it this far, so pat yourself on the back!

If you want to learn more, the [official documentation](https://git-scm.com/doc) is an excellent place to start.

I hope you now have some idea of how to use Git, given that you have some understanding of what's going on apart from the output you see from the commands. I'd urge you to use Git for your personal projects (if you don't already), so you can get used to using this awesome tool!

Thank you for your time, and I hope I've been of some help!

Oh, and don't shy away from telling your friends about this if it helped you :D

PS: I'd love to hear from you if you found this helpful - reach out through my twitter or email (found at the bottom of the page). Thank you!
