# Git Basics
Git is a powerful but lightweight version control system with a lot of features. As all the commands, and especially the
specific direction of these commands can be daunting at first, this guide (and cheatsheet) tries to shed some light on
it. It is recommended to first try this on your own private Github repository to get a feel of what these commands do.
We have tried to build it up from the basics and keep it elemental, for more advanced users this might all be familiar 
already. In either case, [the documentation](https://git-scm.com/docs/) is a good place to look at when you want to know 
the details/accepted flags of certain commands.

## IDE
In order to have an efficient workflow, it is important to have an environment where you can easily develop code. IDEs 
(Integrated Development Environment) provide such a thing. Whether you want a full range of options or a light weight 
solution, these programs make it easier to write and manage code. Examples of full equipped IDEs are JetBrains' PyCharm
or Microsoft's Visual Studio. These editors furthermore provide full Git integration More lightweight editors include 
Visual Code or (with a much steeper learning curve) VIM. Check them out and find your (groups) preferred way to develop!

## Set-up
First, it is needed to install Git for your OS. In Windows, this can be done through 
[their website](https://git-scm.com/download/win), whereas in Debian/Ubuntu Linux `apt-get install git`
suffices (see [here](https://git-scm.com/download/linux) for other distributions).

The first thing you want to do is set-up your user information to be used when reviewing version history:
```
git config --global user.name "firstname lastname"
git config --global user.email "valid-email"
```

What you can do with Git now is creating a folder where git will keep track of its contents, this is called a 
repository. These contents can either be made (from scratch) by you, for this you want an empty folder to start with 
using:

```git init``` 

It is also possible to grab code from an existing repository and keep track of that (remotely) in your folder. For this
you would use:
 
```git clone ssh://user@domain.com/repo.git```

which uses SSH to fetch the repository with code 
(and hence needs a pair of public/private keys to communicate with the server which you need to generate and add 
yourself). Git also allows to sync with a repository over HTTPS, for this you would use 
`git clone https://domain.com/repo.git`, where your password can be cached using a 
[credential helper](https://help.github.com/en/github/using-git/caching-your-github-password-in-git). 
A more extensive overview of allowed protocols and options can be found 
[here](https://www.git-scm.com/docs/git-clone#_git_urls_a_id_urls_a).

## Changes
Now that you have a working directory where your repository is being tracked (either with existing code, or your own), 
you can start to make changes to the files in that folder (using your favorite editor)0. These changes will then be 
automatically recorded by Git. Using the commandline, navigate to the folder you want to check out and have already 
initialised Git in (you can 'add' Git to as many folders as you like). Now you can check the status of your local 
changes with respect to the 'origin' (i.e. the code on the server, where you as a team work on). This is done with:

```git status```

When you are happy with your changes, and want to get these onto the server, you need to 'commit' your changes. Git 
provides a way to document these 'commits' so that when something breaks, you can (more) easily track back to when the 
code did work. First, you need to 'add' your local changes to a specific commit. This is done with:

```git add <file>```

Although it is not recommended to add a lot of files to one commit, as you want the commits to be as 'atomic' (small) as 
possible making it easier to see how the code is changed with that commit, it is possible to add all changed files to 
a commit using:

```git add .```

Now, it is possible to finalise your commit with a useful message. The message should be simple and descriptive of the
 changes made, but still be short as not to clutter the overview of commits. This can be done with:

```git commit -m"Add useful description of changes (roughly <50 characters)"```

If you - in the meantime - needed to add some code which should be included in that commit, you can 'add' the files 
again and use the following command to change your last commit (NB: Don't ammend published commits!):

```git commit --ammend```

Finally, you can get these changes onto the remote server with:

```git push```


## Branches and organization
All the previous commands would work on the default place in Git, the 'master' branch. In Git, a branch can be seen as 
a 'train track' branching off from a code base where development will not interfere with the code on the main 'master' 
branch. This way, you can develop features parallel to working code on the 'master', and later merge these into 'master'
once you have tested them to work. Therefor, it is not recommended to push your changes straight to this master branch.

For this, you can create branches. Again, it is important to make these names descriptive. A rule of thumb is to prepend
the branches with '/feature/', '/fix/', or '/test/'. This makes it easier to make sense of (filter) the branches when 
you have a lot of them. Furthermore, although using `git blame <file>` reveals who changed what, it is 
recommended to append the branch name with your initials. An example from John Doe fixing the communication with an app
would then be:

```/fix/app_communication_jd```

To create branches, it is always good to make sure your current workspace is up to date with the code on the server.
This can be done with:

```git pull```

Then, it is possible to create a branch (assuming you are still on the 'master' branch, else switch to that branch and
pull again using the command explained later). This can be done with:

```git checkout -b </prepend/branch_name_initials>```

This sets up a branch both locally and on the remote (i.e. server) and switches to it. If you want to switch to an 
existing branch, drop the `-b` flag.


## Remotes
The above assumes you have set up only one remote with which git communicates (e.g. only your own Github account), 
however, Git makes it possible to have multiple remote locations and can switch between them. This requires some 
extra arguments in the previous commands, and introduces some new.

Firstly, you can list all the added remotes with:

```git remote -v```

And show information about a specific remote with:

```git remote show <remote>```

It is now possible to add a new remote repository using the following. This is also done 'behind-the-scenes' when you 
for example use `git clone`.

```git remote add <shortname> <url>```

Now you can download all changes from the remote, but not yet integrate them into your local files specified with HEAD
(more on that later).

```git fetch <remote>```

If you do want them integrated into your local Git HEAD, you can use the following:

```git pull <remote> <branch>```

Conversely, you can also push your local commit(s) to a specific remote + branch:

```git push <remote> <branch>```


## Merging and HEAD
The second to last command (`git fetch`) only grabs the changes from the server, but does not include them 
yet. For this, you need to merge these changes into your current HEAD. This HEAD is basically a file which tracks all
the changes you have made locally. 

```git merge <branch>```

Graphically, it looks like this before merging:
```
	  A---B---C branch
	 /
    D---E---F---G current
```
After merging:
```
	  A---B---C branch
	 /         \
    D---E---F---G---H current
```

If, in a more complicated situation, the remote 'master' is ahead of your current branch, you can perform a rebase to
get the commits from master, and then re-apply the changes of your branch. This helps if there is a bug for example, 
which is fixed in the 'master' branch after you've created your branch. If you need to have this fix as well, you need
to perform a rebase, which graphically looks like this:

Before `git rebase master`:
```
          A---B---C topic
         /
    D---E---F---G master
```

After `git rebase master`:
```
                  A'--B'--C' topic
                 /
    D---E---F---G master
```

Inevitably, there will be conflicts between the files you have changed and the files changed on the remote (lucky you
if there aren't any!). These can be resolved manually by adding/removing edited conflicted files using:

```git add <resolved-file>```

```git rm <resolved-file>```

However, it is oftentimes easier to use a graphical tool to solve the conflicts. You can configure any you like (or any 
built into your IDE) using `git config merge.tool <mergetool>`. Common graphical ones are `kdiff3` or 
`meld`. Now, you can resolve conflicts using:

```git mergetool```

## Undo
A great benefit of using Git is that you can undo/roll-back changes you have (erroneously or not) made. Be really 
careful with the use of the commands below though, because using the flag `--hard` cannot be undone!

When you want to discard all local changes in your directory, use:

```git reset --hard HEAD```

Which can also be done for a specific file using:

```git checkout HEAD <file>```


Now, if you want to go back to a certain commit, there are two ways. One is to create a new commit undoing all the 
previous commits. This is done using:

```git revert <commit>```

Otherwise, when you want to reset your HEAD to a previous commit and throw away all your commits, use:

```git reset --hard <commit>```

Resetting your HEAD, but preserve all changes in those commits as unstaged changes:

```git reset <commit>```

Or finally, resetting your HEAD, preserve all changes in those commits as unstaged changes and preserve local 
uncommitted changes:

```git reset --keep <commit>```