# Branching

Branching is the way you can make a copy of the code repository, make changes to it over time, and 
then, when all the functionality and tests are completed, merge those changes back with the main
copy of the repository (usually master).

## Creating a branch in the local repository

The first step is to create a branch in the local reposoitory. This is trivally done with *git branch*. For example, here
we create a branch named "feature3"

```
$ git branch
* master

$ git branch feature3

$ git branch
  feature3
* master
```

First we look and see we only have on branch, *master*. It is the branch we are currently uses as 
denoted with the `*`. Then we create a branch named feature3 and check again. The branch has been 
created, but we are not using that branch yet. 

## Switching to a branch in the local repository

After creating a branch in your local repository, you can use *git checkout* to 'checkout' that branch. 

```
$ git branch
  feature3
* master
$ git checkout feature3
Switched to branch 'feature3'
$ git branch
* feature3
  master
```

## Making changes and commiting them to your local branch

Changes made to the branch are done in the same way as mentioned in lesson 1. 

## Pushing your branch to the remote repository

After you are done with your changes, you need to push your branch to the remote 'origin' repository. 

This is necessary to create a 'pull request' or 'merge request'. The terminoloty changes depending
on the git provider you use. 

It is realtivly easy to push a branch to the remote repository. Use the *git push* command and specify
the remote you are pushing to and the branch name. 

```
$ git push --set-upstream origin feature3
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (7/7), 632 bytes | 0 bytes/s, done.
Total 7 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
remote: 
remote: Create a pull request for 'feature3' on GitHub by visiting:
remote:      https://github.com/danielcaldwell/test_git/pull/new/feature3
remote: 
To git@github.com:danielcaldwell/test_git.git
 * [new branch]      feature3 -> feature3
Branch feature3 set up to track remote branch feature3 from origin.
```



# Merging Changes from master into your feature branch

## Use Rebasing to bring commits from master to your feature branch

As changes to the master occur while you are developing, it is good to bring those changes into your branch in order to keey your development up to date with the latest source code. The longer you wait, the more out of date it will be and the more difficult to resolve conflicts it will become. 

First, change to your master branch and pull the latest code. There should be no conflicts as you have only made changes in your feature branches. 

```
git checkout master
git pull
```

For example: 
```
$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.

$ git pull
Already up-to-date.
```

Next, change to your feature branch and do a rebase against master. 
```
git checkout feature1
git rebase master
```

Here we can see the log entries showing the changes: 
```
$ git log --graph --oneline --all
* 329a0a5 master update 9
* c8f23ea master update 8
* c488f9e master update 7
* c141f08 master update 6
| * 2662fee feature 1 update 7
| * 95984da feature 1 update 6
| * a1d6ea0 feature 1 update 5
| * 22632e1 feature 1 update 4
| * b96bfae feature 1 update 3
| * 4cb358c feature 1 update 2
| * d44e921 feature 1 update 1
|/  
* 094b41e master update 5
* 3958b79 master update 4
* d8cbd8e master update 3
* 3f81757 master update 2
* ff23269 master update 1
* 922414d initial commit
```

we want to rebase master to our feature, which will bring updates 6,7,8,9 into feature1

```
$ git checkout feature1
Already on 'feature1'
Your branch is up-to-date with 'origin/feature1'.

$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: feature 1 update 1
Applying: feature 1 update 2
Applying: feature 1 update 3
Applying: feature 1 update 4
Applying: feature 1 update 5
Applying: feature 1 update 6
Applying: feature 1 update 7
Daniels-MacBook-Pro:test_git dcaldwel$
```

We've pushed our changes beforehand, so we will see a divergent message indicatign that the feature1 branch on the remote origin has diverged from our local branch. 
We need to do a force push to push the rebase to the remote origin. This is caused because we have rewritten the history of the branch and is the reason why rebasing
only works well in isolated branches. 

```
$ git status
On branch feature1
Your branch and 'origin/feature1' have diverged,
and have 11 and 7 different commits each, respectively.
  (use "git pull" to merge the remote branch into yours)
nothing to commit, working directory clean

$ git push -f 
Counting objects: 21, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (19/19), done.
Writing objects: 100% (21/21), 1.88 KiB | 0 bytes/s, done.
Total 21 (delta 4), reused 0 (delta 0)
remote: Resolving deltas: 100% (4/4), done.
To git@github.com:danielcaldwell/test_git.git
 + 2662fee...24ad9fe feature1 -> feature1 (forced update)
```

Now we can check the log to see that the updates from master were applied to our feature branch: 

```
Daniels-MacBook-Pro:test_git dcaldwel$ git log --graph --oneline --all
* 24ad9fe feature 1 update 7
* a7862c2 feature 1 update 6
* aafb886 feature 1 update 5
* a1ebe17 feature 1 update 4
* 7cefa0b feature 1 update 3
* 6dcf05c feature 1 update 2
* 252ec7b feature 1 update 1
* 329a0a5 master update 9
* c8f23ea master update 8
* c488f9e master update 7
* c141f08 master update 6
* 094b41e master update 5
* 3958b79 master update 4
* d8cbd8e master update 3
* 3f81757 master update 2
* ff23269 master update 1
* 922414d initial commit
```

if we make a change in the master, then look at the log we will see the graph again. 

```
$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.

$ echo "(master update 10)" >> master.txt; git add master.txt; git commit -m "master update 10"; git push;
[master 31aba59] master update 10
 1 file changed, 1 insertion(+)
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 283 bytes | 0 bytes/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To git@github.com:danielcaldwell/test_git.git
   329a0a5..31aba59  master -> master

$ git log --graph --oneline --all
* 31aba59 master update 10
| * 24ad9fe feature 1 update 7
| * a7862c2 feature 1 update 6
| * aafb886 feature 1 update 5
| * a1ebe17 feature 1 update 4
| * 7cefa0b feature 1 update 3
| * 6dcf05c feature 1 update 2
| * 252ec7b feature 1 update 1
|/  
* 329a0a5 master update 9
* c8f23ea master update 8
* c488f9e master update 7
* c141f08 master update 6
* 094b41e master update 5
* 3958b79 master update 4
* d8cbd8e master update 3
* 3f81757 master update 2
* ff23269 master update 1
* 922414d initial commit
```

This is one way to get changes from your master branch into your feature branch. 


# Preparing a pull request by Squashing Commits with Rebasing

## Finding the sha of the first commit we made to our branch

We can take a look at our log for our branch. 

``` 
$ git log --oneline
6c2953b feature 2 update 7
5444bfd feature 2 update 6
8a39be2 feature 2 update 5
e7177ca feature 2 update 4
ac557e3 feature 2 update 3
8cbda0a feature 2 update 2
37271d0 feature 2 update 1
4a0321f master update 9
5864d8a master update 8
63061c3 master update 7
e8cfd8c master update 6
2ade829 master update 5
4232485 master update 4
287a9ad master update 3
f8fdb3a master update 2
7b5bada master update 1
```

The sha hash of the common ancestor to master is 4a032f... we will use that one to sqash the commits into one. But what do we do if this is a long list or if the labeles don't help
us determine what is the right one? We just have to ask *git log* with the proper parameters. This command gets the parents of all the commit entries between master and the feature branch, and then grabs the last one. 

```
$ git log --pretty=%P master..feature2 | tail -1
4a0321f447d542586c173f0f30939ed474fabb89
```

### Rebase to that commit

```
git rebase -i 4a0321f447d542586c173f0f30939ed474fabb89
```

It will open a terminal where you can choose which commits to squash. ** Note that the commits are reversed from *git log* **

```
  1 pick 37271d0 feature 2 update 1
  2 pick 8cbda0a feature 2 update 2
  3 pick ac557e3 feature 2 update 3
  4 pick e7177ca feature 2 update 4
  5 pick 8a39be2 feature 2 update 5
  6 pick 5444bfd feature 2 update 6
  7 pick 6c2953b feature 2 update 7
  8 
  9 # Rebase 4a0321f..6c2953b onto 4a0321f (7 command(s))
 10 #
 11 # Commands:
 12 # p, pick = use commit
 13 # r, reword = use commit, but edit the commit message
 14 # e, edit = use commit, but stop for amending
 15 # s, squash = use commit, but meld into previous commit
 16 # f, fixup = like "squash", but discard this commit's log message
 17 # x, exec = run command (the rest of the line) using shell
 18 # d, drop = remove commit
 19 #
 20 # These lines can be re-ordered; they are executed from top to bottom.
 21 #
 22 # If you remove a line here THAT COMMIT WILL BE LOST.
 23 #
 24 # However, if you remove everything, the rebase will be aborted.
 25 #
 26 # Note that empty commits are commented out
```

We squash all the ones except for the first one as we want to keep all the changes and just merge them into the first one. 

```
  1 pick 37271d0 feature 2 update 1
  2 squash 8cbda0a feature 2 update 2
  3 squash ac557e3 feature 2 update 3
  4 squash e7177ca feature 2 update 4
  5 squash 8a39be2 feature 2 update 5
  6 squash 5444bfd feature 2 update 6
  7 squash 6c2953b feature 2 update 7
```


It then opens an editor where you can change the commit messages. I change it to have the final message I want, then add 
a "Squashed Commits: " message for all the others. 

```
  1 # This is a combination of 7 commits.
  2 feature 2 fixed
  3 
  4 Squashed Commits: 
  5 
  6 # The first commit's message is:
  7 feature 2 update 1
  8 
  9 # This is the 2nd commit message:
 10 
 11 feature 2 update 2
 12 
 13 # This is the 3rd commit message:
 14 
 15 feature 2 update 3
 16 
 17 # This is the 4th commit message:
 18 
 19 feature 2 update 4
 20 
 21 # This is the 5th commit message:
 22 
 23 feature 2 update 5
 24 
 25 # This is the 6th commit message:
 26 
 27 feature 2 update 6
 28 
 29 # This is the 7th commit message:
 30 
 31 feature 2 update 7
```

Now we can check the log and see only the one commit: 

```
$ git log -n 10 --oneline
2d0887e feature 2 fixed
4a0321f master update 9
5864d8a master update 8
63061c3 master update 7
e8cfd8c master update 6
2ade829 master update 5
4232485 master update 4
```

Now we are good to go. If you have pushed your commits before you can push again, but you will need to force it
as you have rewritten the history of the branch. Also, anyone else will have lots of problems if they are developing off of your branch. 
This is great for individual branches, but not shared ones. 



## Bring feature branches changes into master

Once you have done a rebase you have brought all of the master commits into the feature branch. Putting the feature branch's 
commits onto master is trivial as all conflicts have been already handled. We can just do a merge and push at that time. 

```
git checkout master
git merge feature1
git push
```

# Cleanup and Deleting unneeded branches

After your feature branch has been merged to the master branch, you may no longer need them. So, you can delete it. This is how you 
delete it on your local repository and remote repository. 


## Delete the local branch
```
git branch -D <branch name>
```

## Delete the remote branch
```
git push --delete origin <branch name>
```


# Troubleshooting

## Deleting the branch when it has been deleted on the remote

This can occur when you create a pull request on Github or Gitlab and choose to delete the branch from there. If you subsequently try to delete the branch
from the remote, it will throw an error. But it will still appear in your branch list. So you can use *git fetch --prune* to remove those. 

```
$ git branch -a
* feature2
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/feature1
  remotes/origin/feature2
  remotes/origin/master
Daniels-MacBook-Pro:test_git dcaldwel$ git fetch --prune
From github.com:danielcaldwell/test_git
 x [deleted]         (none)     -> origin/feature1
Daniels-MacBook-Pro:test_git dcaldwel$ git branch -a
* feature2
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```

## How do I delete all of my commits including the very first commit. 

We follow these steps: 
1. Delete the branches
2. Reset back to the first commit
3. Delete the HEAD reference
4. Make a commit of something
5. Force Push that commit

```
git branch -D local_branch
...

git push --delete origin remote_branch
...

git reset #####
git push -f

git update-ref -d HEAD
``` 
