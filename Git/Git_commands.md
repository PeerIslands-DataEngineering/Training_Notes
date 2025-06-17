## git init: Initializes a new Git repository in the current directory.


```
$ git init

Initialized empty Git repository in /path/to/your/project/.git/
```

## *git clone*: Clones an existing Git repository.

```
$ git clone https://github.com/user/repository.git


Cloning into 'repository'...
```

## *git add*: Adds files to the staging area.

```
$ git add file1.txt file2.txt
$ git add .  # Adds all files in the current directory
```

## *git commit*: Commits the changes in the staging area.

```
$ git commit -m "Add new feature"

[master 1234567] Add new feature
2 files changed, 10 insertions(+)
```

## *git push*: Pushes the committed changes to a remote repository.

```
$ git push origin master


Counting objects: 10, done.
Compressing objects: 100% (6/6), done.
Pushing objects: 100% (10/10), 1.25 MiB | 1.00 MiB/s, done.

```

## *git pull*: Pulls the latest changes from a remote repository.

```
$ git pull origin master


remote: Counting objects: 5, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 5 (delta 2), reused 5 (delta 2)
Unpacking objects: 100% (5/5), done.
From https://github.com/user/repository
master -> origin/master
Updating 1234567..abcdef
Fast-forward
file1.txt | 2 +-
1 file changed, 1 insertion(+), 1 deletion(-)
```

## *git status*: Shows the current status of the repository.

```
$ git status


On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   file1.txt
    modified:   file2.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

    file3.txt
```

## *git log*: Shows the commit history of the repository.

```
$ git log


commit abcdef1234567890abcdef1234567890abcdef1
Author: Kaveripriya <kaveri.priya@revature.com>
Date:   Mon Apr 8 10:00:00 2024 +0000

    Add new feature

commit 1234567890abcdef1234567890abcdef1abcdef
Author: Kaveripriya <kaveri.priya@revature.com>
Date:   Fri Apr 5 15:30:00 2024 +0000

    Fix bug
 ```
   
## *git branch*: Lists, creates, or deletes branches.

```
$ git branch

  master
* develop

$ git branch feature-x
$ git branch -d feature-x
```

## *git merge*: Merges changes from one branch into another.

```
$ git checkout master
$ git merge develop

Updating abcdef1..1234567
Fast-forward
 file1.txt | 2 +-
 file2.txt | 4 ++++
 2 files changed, 5 insertions(+), 1 deletion(-)
```
