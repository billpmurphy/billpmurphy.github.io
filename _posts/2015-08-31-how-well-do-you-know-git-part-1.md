---
layout: post
title: How well do you know git? (part 1)
---

Git has a pretty confusing UI, and it can be hard to remember all of the
commands. To test your knowledge, I took all of the code samples from the
excellent book [Pro Git](https://git-scm.com/book/en/v2) and turned them into
quiz questions.

This post covers basic Git (chapters 1, 2, and 3). If you can figure out most
of these command without Googling, you should feel pretty good about your git
skills! If you just want to use this as a reference, all the answers are at the bottom.


**Chapter 1**

1. Set the global git username to "John Doe" and the global email to
   "johndoe@example.com".
2. Set the default text editor to Emacs.
3. View the current git configuration settings.
4. View the current git username.
5. View the manpage for `git config`.


**Chapter 2**

1. Create a repository in the local directory. On the first commit, add all of
   the Python files in the local directory to the repo.
2. Clone the repository `http://github.com/git/git` to the local directory
   `my_git`.
3. View which files are in the staging area and which are untracked.
4. Write a gitignore file that ignores all files ending in `.o` or `.a` and
   files in the `build` directory, but does not ignore `lib.a`.
5. Write a gitignore file that ignores all pdf files in all subdirectories of
   the `doc` directory and the file `TODO` in the current directory (but not
   the `TODO` subdirectory).
6. View changes that have not yet been staged.
7. View changes that have been staged.
8. Stage and commit all of the files that are already being tracked.
9. Remove `file.c` from the git repository in the current directory.
10. Remove `file.c` from the git repository in the current directory, assuming
    `file.c` has already been added to the index.
11. Remove `file.c` from the git repository in the current directory, without
    deleting it from the current directory.
12. Move `file.c` to `my_file.c` and commit the change.
13. View the commit history and...
    1. the diff from each commit.
    2. the statistics for files modified in each commit.
    3. an ASCII graph of the branch and merge history.
14. View only the commits...
    1. made in the last two weeks.
    2. made by author "John Doe".
    3. that added or removed code including the string "hash_func".
15. Undo the last commit, and create a new commit using files in the staging
    area.
16. Unstage a file `README.md`.
17. Discard changes to `README.md`, reverting it back to how it was on the
    previous commit.
18. View configured remotes for the current repository, and the urls for each
    remote.
19. Add a remote named `pb` with the url `http://example.com/pb.git`.
20. Pull down all data from the remote `pb`.
21. Push the branch `master` to the remote `pb`.
22. Show information about the remote `pb`.
23. Rename the remote `pb` to `paul`.
24. Remove the remote `paul`.
25. View all tags in the `v1.8.5` series.
26. Create a new tag `v1.4` with the message "Beta Version".
27. Show information about the tag `v.1.4`.
28. Create a new lightweight tag `v1.2` on the commit `9fceb02` and then push
29. Push all tags to `origin`.
30. Add an alias `last` that runs `git log -l HEAD`.


**Chapter 3**

1. Create a new branch called `testing` in the current repository, then
   checkout that branch.
2. Merge the `testing` branch back into `master`.
3. Delete the `testing` branch.
4. Push the branch `testing` to the remote `origin`.
5. Check out the branch `testing` at the remote `origin` to a local branch
   called `testing2`.
6. Create a local tracking branch for the branch `testing` on the remote
   `origin`.
7. List information about all local branches.
8. Delete the branch `hotfix` on the remote `origin`.
9. Rebase the branch `server` onto the branch `master.


--------

### Answers


**Chapter 1**

1) Set the global git username to "John Doe" and the global email to
"johndoe@example.com".

```
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

2) Set the default text editor to Emacs.

```
git config --global core.editor emacs
```

3) View the current git configuration settings.

```
git config --list
```

4) View the current git username.

```
git config user.name
```

5) View the manpage for `git config`.

```
git help config
```


**Chapter 2**

1) Create a repository in the local directory. On the first commit, add all of
the Python files in the local directory to the repo.

```
git init
git add *.py
git commit -m "initial commit"
```

2) Clone the repository `http://github.com/git/git` to the local directory
`my_git`.

```
git clone http://github.com/git/git my_git
```


3) View which files are in the staging area and which are untracked.

`git status` or `git status -s`


4) Write a gitignore file that ignores all files ending in `.o` or `.a` and
files in the `build` directory, but does not ignore `lib.a`.

```
*.[oa]
build/
!lib.a
```


5) Write a gitignore file that ignores all pdf files in all subdirectories of
the `doc` directory and the file `TODO` in the current directory (but not the
`TODO` subdirectory).

```
doc/**/*.pdf
/TODO
```


6) View changes that have not yet been staged.

```
git diff
```


7) View changes that have been staged.

`git diff --staged` or `git diff --cached`


8) Stage and commit all of the files that are already being tracked.

```
git commit -a -m "committing already tracked files"
```


9) Remove `file.c` from the git repository in the current directory.

```
git rm file.c
git commit -m "removed file.c"
```

10) Remove `file.c` from the git repository in the current directory, assuming
`file.c` has already been added to the index.

```
git rm -f file.c
git commit -m "removed file.c"
```


11) Remove `file.c` from the git repository in the current directory, without
deleting it from the current directory.

```
git rm --cached file.c
git commit -m "removed file.c"
```


12) Move `file.c` to `my_file.c` and commit the change.

```
git mv file.c my_file.c
git commit -m "moved"
```


13) View the commit history and...
    a) the diff from each commit.
    b) the statistics for files modified in each commit.
    c) an ASCII graph of the branch and merge history.

```
git log -p
git log --stat
git log --graph
```


14) View only the commits...
    a) made in the last two weeks.
    b) made by author "John Doe".
    c) that added or removed code including the string "hash_func".

```
git log --since=2.weeks
git log --author="John Doe"
git log --S=hash_func
```


15) Undo the last commit, and create a new commit using files in the staging
area.

```
git commit --amend
```


16) Unstage a file `README.md`.

```
git reset HEAD README.md
```


17) Discard changes to `README.md`, reverting it back to how it was on the
previous commit.

```
git checkout -- README.md
```


18) View configured remotes for the current repository, and the urls for each
remote.

```
git remote -v
```


19) Add a remote named `pb` with the url `http://example.com/pb.git`.

```
git add remote pb http://example.com/pb.git
```


20) Pull down all data from the remote `pb`.

```
git fetch pb
```


21) Push the branch `master` to the remote `pb`.

```
git push pb master
```


22) Show information about the remote `pb`.

```
git remote show pb
```


23) Rename the remote `pb` to `paul`.

```
git remote rename pb paul
```


24) Remove the remote `paul`.

```
git remote rm paul
```


25) View all tags in the `v1.8.5` series.

```
git tag -v '1.8.5*'
```


26) Create a new tag `v1.4` with the message "Beta Version".

```
git tag -a v1.4 -m 'Beta Version'
```


27) Show information about the tag `v.1.4`.

```
git show v1.4
```


28) Create a new lightweight tag `v1.2` on the commit `9fceb02` and then push
it to `origin`.

```
git tag v1.2 9fceb02
git push origin v1.2
```


29) Push all tags to `origin`.

```
git push origin --tags
```

30) Add an alias `last` that runs `git log -l HEAD`.

```
git config --global alias.last 'log -h HEAD'
```


**Chapter 3**

1) Create a new branch called `testing` in the current repository, then
checkout that branch.

```
git branch testing
git checkout testing
```

or

```
git checkout -b testing
```


2) Merge the `testing` branch back into `master`.

```
git checkout master
git merge testing
```


3) Delete the `testing` branch.

```
git branch -d testing
```


4) Push the branch `testing` to the remote `origin`.

```
git push origin testing
```


5) Check out the branch `testing` at the remote `origin` to a local branch
called `testing2`.

```
git checkout -b testing2 origin/testing
```


6) Create a local tracking branch for the branch `testing` on the remote
`origin`.


```
git checkout --track origin/testing
```


7) List information about all local branches.

```
git branch -vv
```


8) Delete the branch `hotfix` on the remote `origin`.

```
git push origin --delete hotfix
```


9) Rebase the branch `server` onto the branch `master.

```
git checkout server
git rebase master
```

or

```
git rebase master server
```
