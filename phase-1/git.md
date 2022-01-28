# Setting up Git

Install Git by typing the following into the terminal:

```
sudo apt update
sudo apt install git -y
```

Configure Git with the following commands, replacing `<email>` with your email and `<name>` with your name. This information would be included in all your commits.

```
git config --global user.email "<email>"
git config --global user.name "<name>"
```

You should also set up SSH access with Git. Run the following command to generate an SSH key:

```
ssh-keygen
```

Accept the default key location and enter a password you'll remember. Depending on your threat model, you can also just leave the password empty; it'll only be used to encrypt a key that won't leave your machine.

Next, go to GitHub, click your user icon on the top right, click `Settings`, then click `SSH and GPG keys` on the left navigation bar. Click `New SSH key`, open `~/.ssh/id_rsa.pub` in a text editor (you can do this by running `xdg-open ~/.ssh/id_rsa.pub` in the terminal) and copy its contents into the `Key` box. Give it any title, and click `Add SSH key`.

# Using Git

There are many different ways to use Git with different workflows more suited for different kinds of projects. Here we'll describe the rebase workflow, which is appropriate for projects where multiple people contribute to a single "main branch" (e.g. your compiler!).

First, let's think about what we want to achieve on a conceptual level. The "single source of truth" for your project, i.e. the "main branch", is maintained by GitHub. When you make a change, you want to "check out" the latest project code, edit it, then "commit" the change into the main branch.

Unfortunately, this workflow is beset by several problems, illustrated by the following scenarios:

1. Your partner committed a change after you have checked out the main branch and are in the midst of preparing another change. Now you have to check out the code again, figure out what changes you made since the previous checkout, and transplant those changes to the updated copy.
2. You're implementing feature A which requires some big changes to the code. Right now, you're only halfway done and the code wouldn't work at all until you've finished the change, but you really want to work on feature B right now. You'll need to check out a new copy of the main branch, implement feature B, and then integrate your work on feature A with your work on feature B.
3. Your partner committed a change without testing and now everything is broken. You don't have time to debug it right now so you'd like to undo to a previous version of the code without losing your partner's work.

Git doesn't avoid or substitute for doing that work, it just makes it easier, e.g. by tracking what changes you've made so that you can transplant them easily.

## Git basics

If you'd like to follow along, create a new repository on GitHub (click the `+` at the top right, enter any repository name, probably set it to private, and untick everything under `Initialize this repository with:`. Then get the URL of the repository in the `Quick setup` box. This will hold our "main branch". Navigate to `~/Documents` (or wherever you'd like to put the tutorial files) and run the following commands to initialize the main branch with the course `java-skeleton` code:

```
git clone https://github.com/6035/java-skeleton.git example-project
cd example-project
git remote set-url origin <your new github repo url>
git push origin master
```

Let's look at what this does step-by-step. The first command, which has the format `git clone <url> <dir>`, creates a new directory named `<dir>` and downloads the Git repository from `<url>` into it. A Git repository doesn't just contain code, it also contains information like the _commit history_, which is a log of all the changes that people have committed to it.

This information is stored in a special directory called `.git` located in the project directory. It's there if you run `ls -a` (which lists all directory contents, including hidden ones). In fact, everything Git generates to manage a repository is contained in the `.git` directory, so you can freely move and delete Git-managed project directories without worrying about broken links or cruft in the rest of your system.

A repository on the Internet that you can download and push to is called a _remote_ (as opposed to a _local repository_, like the cloned copy you just made that's living on your computer). When you did the `git clone`, Git set up a remote called `origin` pointing to the `java-skeleton` repository. We used `git remote set-url` to make `origin` point to your personal repository instead.

The `git remote` family of commands is useful to know when working across multiple remotes. For example, you will encounter them again when you transfer your phase 1 repository to your team repository. For the most part, though, you will only be interacting with one main repository, so we won't go into remotes any further here.

The last command, `git push`, copies the `master` branch from your local repository to the `origin` repository, i.e. your GitHub repository. We'll go into more detail about `git push` later; first, we'll explain what the `master` branch is.

A repository can hold multiple _branches_ of code. One of these branches is designated as the _main branch_ (not to be confused with the main branch of your project) and is called `master` by default. Git recommends renaming this to `main`; you can do this by running `git branch -m main`, and, if you already ran the `git push` command, changing the branch name on Github under `Settings > Branches` in the repository page. The first step renames the branch in your local repository, and the second step renames it in GitHub's copy. The rest of this text will continue referring to the main branch as `master`, but if you've renamed the branch, just replace all occurrences of `master` with `main`.

Git branches are very powerful, but you won't need to use more than one branch for this project. For this reason, the main branch of your project will be precisely the main branch of the GitHub repository you created, i.e. the `master` branch of the `origin` remote. So when you see `origin` and `master` together in subsequent commands, you know that we're referring to your project's main branch, but keep in mind that those commands can be applied to different remotes and different branches.

## Exploring the commit history

We mentioned that the _commit history_, i.e. the list of all changes (called _commits_) committed to a branch, is stored in `.git`. We can read it with the following command:

```
git log
```

Use the up and down arrow keys to navigate the log, and hit `q` to exit it. The commits are sorted from latest to earliest. From the interface, you can see that each commit comes with a name and email identifying who made the commit, and a _commit message_ describing what the change does. There is also a long hexadecimal string on the first line of each commit, called the _commit hash_.

These hashes allow us to identify particular commits when talking to Git. Though, it's better to think of a has as referring to the _point of time_ just after a commit was made, called the `commit point`. For example, pick any two consecutive commit hashes from the log and run `git diff <earlier hash> <later hash>`. This displays the changes made by the later commit. In general, `git diff <commit 1> <commit 2>` shows the changes made between any two commit points.

We can also "travel back" to an earlier commit point. Pick any hash (other than the first one) and type `git checkout <hash>`. You'll get a long message about being in a `detached HEAD` state, but the files in your filesystem should now reflect the code at the chosen commit point. `HEAD` refers to your "current working commit point", so `detached HEAD` just means that your current commit point isn't at the tip of a named branch (i.e. `master`). Return to the tip of `master` by typing `git switch master`.

## Making commits

Let's make a change. Edit any file with a text editor. Now, type the following command:

```
git status
```

It should say `modified: <file>` (where `<file>` is the file you edited), showing that Git remembers what changes you made. You can see the exact changes made with `git diff`.

Now let's commit it to the project main branch. In Git, this is a two-step process. This is because Git users tend to like having a _clean commit history_. For example, if you implement two distinct features in different parts of your code, it's good practice to commit them as two separate commits even if you wrote both of them at the same time. This makes it easier to "undo" one of them in the future if something breaks, say. You don't have to worry about a clean commit history unless someone else on your team does.

For now, we'll be committing all our changes as a single commit. First, we need to designate what we want in our commit. Tell git that we want to mark (or, in Git's terminology, stage) everything for commit with the following command:

```
git add -A
```

Now, if you do `git status`, the line `modified: <file>` has turned green, indicating that that change is staged for commit. For colorblind users, it also now says `Changes to be committed:` instead of `Changes not staged for commit:`. At this point, the changes have not been committed yet. Actually create the commit with the following command:

```
git commit -m "make a change"
```

Here, `make a change` is the commit message we're assigning to the commit. It should describe the changes you've made so that, if say something breaks in the future and you want to undo this commit, it'd be easy to find just by looking at the commit history with `git log`.

At this point, the commit has been created on your local branch, but not on GitHub's copy. To propagate your commits to the project main branch, run the following command:

```
git push origin master
```

Having this step separate allows you to prepare multiple commits before touching the main branch.

## Ignoring files

There are some files you don't want to commit. For example, you don't want to commit your compiler binary itself. It takes up space on the main branch, makes Git slower, and creates unnecessary merge conflicts (a concept we'll explain later). One way to deal with this is to `git add` only the source files you've changed instead of using `git add -A`, but this can become very tedious. Git's solution for this is the `.gitignore` file.

A `.gitignore` file contains a series of _patterns_, one per line. Any subdirectory of your project can contain a `.gitignore` file; the patterns will be evaluated relative to the position of the file. Git will not track changed files that match a pattern in any `.gitignore` file. This means that the changed file would not appear in `git status` or `git diff`, and would not be added when you do `git add -A`.

Documentation for how patterns work can be found in the [gitignore documentation](https://git-scm.com/docs/gitignore), but here are some common examples:

- `.DS_STORE` would match files or directories called `.DS_STORE` in the same directory or any descendent directory of where the `.gitignore` file is located. MacOS creates `.DS_STORE` files in every directory you open with Finder. Since these files are unrelated to your project, this would be a useful line to include if anyone on your team uses a Mac.
- `.*.swp` would match any file or directory with a name matching `.*.swp` (e.g. `.file.txt.swp`) in the same directory or any descendent directory of where the `.gitignore` file is located. Note that Git treats files beginning with `.`. In this example, `*.swp` would not match `.file.txt.swp`; the `.` must be explicitly prepended. This matches the "swap files" used by Vim for crash recovery, so it would be a useful line to include if anyone in your team uses Vim.
- `/autogen/` would match the directory `autogen` in the same directory where the `.gitignore` file is located. The `/` at the front means that `autogen` must be in the same directory as the `.gitignore` file, while the `/` at the back means that `autogen` must be a directory. Similarly, `/src/autogen/` only matches the directory `autogen` in the `src` subdirectory, but would not match an `autogen` directory in the same directory as the `.gitignore` file. This line appears in `java-skeleton`'s `.gitignore` since Ant uses this directory to store the parser files generated by ANTLR.

We recommend, especially for the first few commits, to always make sure that you do not commit unnecessary files by checking the list of files to be committed with `git status` before making any commits.

## Working with others

Now let's see how to handle scenario 1, where your partner pushes a commit while you're in the middle of making a change. To simulate this, navigate out of your project directory, create another copy of the main branch and navigate to it:

```
cd ..
git clone <url> example-project-partner
cd example-project-partner
```

Now make a change to one line in a file. Then commit it and push it to origin:

```
git add -A
git commit -m "make another change"
git push origin master
```

Now go back to your original project directory:

```
cd ../example-project
```

To simulate the worst-case scenario, edit the _same line_ in the same file. We want to illustrate what happens when your changes _conflict_ with those of your partner. This could happen when both of you are working on the same part of the code at the same time, e.g. if one of you does a refactor. Now attempt to commit it:

```
git add -A
git commit -m "make yet another change"
git push origin master
```

The last command should reject your changes. This is because your local branch does not contain the changes from your simulated "partner". To resolve this, conceptually, you want to grab the latest origin copy and transplant your work, i.e. commits, on top of that. Let's do it!

```
git pull --rebase origin master
```

What this does is download the latest updates from the main branch on GitHub and "transplants", or, in Git's terminology, _rebases_ your local branch onto it. You can imagine Git taking the changes you had just committed and trying to apply it on top of the downloaded main branch. This is illustrated by the following diagram adapted from the [git-rebase documentation](https://git-scm.com/docs/git-rebase):

```
          D master
         /
    A---B---C origin/master

becomes

              D' master
             /
    A---B---C origin/master
```

Unfortunately, in our case, Git encounters a _merge conflict_ trying to apply our latest changes, since we edited the same line as our partner. Run `git status` to find exactly which files encountered merge conflicts.

In our case, only one file has a merge conflict. If you open it, you should see the following text somewhere in the file:

```
<<<<<<< HEAD
... line containing your partner's changes ...
=======
... line containing your changes ...
>>>>>>> make yet another change
```

So how Git does this exactly is to set the filesystem state (and `HEAD` along with it) to the downloaded main branch, and then apply the "make yet another change" commit on top of it. That's why the "old" version (i.e. the lines between `<<<<<<<` and `=======`) is labeled `HEAD`, while the "new" version (i.e. the lines between `=======` and `>>>>>>>`) is labeled with the commit message. In a more complex merge conflict, you might see multiple structures of this form, possibly even in a single file.

To _resolve_ this conflict, simply replace that structure with what you want that line to be in the final version. If there are multiple conflicts, you should resolve all of them before continuing. Once all merge conflicts are fixed, mark them for commit and tell Git to continue with the rebase:

```
git add -A
git rebase --continue
```

Don't commit them! Performing the commits is part of the rebase process.

If you ever get lost or mess up while resolving conflicts, you can reset to the state you were in before the rebase with `git rebase --abort`. When resolving conflicts, it's often helpful to understand what your partner's changes were intended to do, or at least look at the unmerged version of the code on GitHub.

## Further usage

Let's take a look a how to handle scenarios 2 and 3 described earlier. For scenario 2, Git allows you to save uncommitted changes to a _stash_ with the following command:

```
git stash
```

When you want to retrieve them later, type:

```
git stash apply
```

You might have to fix merge conflicts. Note that there isn't a way to "abort" this like in a rebase. Once you're done, remove the changelist from the stash with the following command:

```
git stash drop
```

You can push multiple unrelated changelists to the stash; it works just like a stack. `git stash pop` is shorthand for doing both `apply` and `drop`, but note that it doesn't perform the `drop` if the `apply` encountered merge conflicts.

Finally, consider the scenario where you find that the project is broken and you need to "undo" a bad commit. To do so safely, you usually want to create an "undo" commit that undoes the effects of the bad commit. This can be done with the following command, using the hash of the bad commit:

```
git revert <hash>
```

There's a whole lot more you can do with git with branches, hard resets, and interactive staging/rebasing, but hopefully this gives you the basic tools needed to work effectively on the group project and the background needed to understand more advanced features. If you encounter problems using Git, consult the TAs.

# More resources

- Setting up Git: <https://docs.github.com/en/github/getting-started-with-github/set-up-git>
- More in-depth Git tutorial: <https://git-scm.com/docs/gittutorial>
- Really in-depth Git "tutorial": <https://git-scm.com/book/en/v2/>
- Git documentation: <https://git-scm.com/doc>
- Git non-documentation: <https://git-man-page-generator.lokaltog.net/>
