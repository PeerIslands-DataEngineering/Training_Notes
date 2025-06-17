## git diff: Shows the differences between the working directory, staging area, and the last commit.
```
$ git diff
diff --git a/file1.txt b/file1.txt
index abc123..def456 100644
--- a/file1.txt
+++ b/file1.txt
@@ -1,3 +1,4 @@
 This is file1.
+Added a new line.
 This is another line.
 This is the third line.
```
## git checkout: Switches to a different branch or restores files from the index

```
$ git checkout develop
Switched to branch 'develop'
$ git checkout -- file1.txt  # Restores file1.txt from the index
```
## git reset: Unstages changes or resets the repository to a previous state.

```
$ git reset HEAD file1.txt  # Unstages file1.txt
$ git reset --hard HEAD~3  # Resets the repository to 3 commits ago
```
## git rm: Removes files from the working directory and the index.

```
$ git rm file1.txt
rm 'file1.txt'
```
## git rebase: Reapplies commits from one branch onto another.

```
$ git checkout feature-x
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: Add new feature
```
## git fetch: Downloads objects and refs from a remote repository.

```
$ git fetch origin
remote: Counting objects: 5, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 5 (delta 2), reused 5 (delta 2)
Unpacking objects: 100% (5/5), done.
From https://github.com/user/repository
   1234567..abcdef  master     -> origin/master
```

## git commit --amend: Modifies the most recent commit.

```
$ git commit -m "Add new feature"
$ git commit --amend -m "Add new feature with improvements"
```
