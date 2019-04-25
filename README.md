# basic_github_tutorial
Basic tutorial on git + hosting service (e.g. Github) for people collaborating on a shared project.

## What is pull and push?
*Remark*: I encourage you to run the `git status` command at every step of this tutorial to better understand what git does.

Imagine you decide to *create* a new file called _file1.txt_ and you want to share it with colleagues. You should first create it:
```bash
$ echo "Toto goes to the beach" > file1.txt

$ git status
> On branch master
>
> No commits yet
>
> Untracked files:
>   (use "git add <file>..." to include in what will be committed)
>
>	 file1.txt
>
> nothing added to commit but untracked files present (use "git add" to track)
```
Then, you would like to *add* the file to the index of git
```bash
$ git add file1.txt

$ git status
> On branch master
>
> No commits yet
>
> Changes to be committed:
>   (use "git rm --cached <file>..." to unstage)
>
>	 new file:   file1.txt
>
```
Then you should *commit* this change, which means to record changes to the repository.
```bash
$ git commit -m "new file created"
> [master (root-commit) b3b2b99] new file created
>  1 file changed, 1 insertion(+)
>  create mode 100644 file1.txt

$ git status
> On branch master
> Your branch is based on 'origin/master', but the upstream is gone.
>   (use "git branch --unset-upstream" to fixup)
>
> nothing to commit, working tree clean
```
Note that you should always try to write clear and concise commit messages (after the `-m` argument) in order to make life easier for you co-workers.

Now, your changes have been recorded locally, but you want to share them with your colleagues. So, let's *push* them on the repository hosted online.
```bash
$ git push
> Writing objects: 100% (3/3), 247 bytes | 247.00 KiB/s, done.
> Total 3 (delta 0), reused 0 (delta 0)
> To github.com:ngazagna/example.git
>  * [new branch]      master -> master
```

Finally, imagine that one of your colleagues wants to get access to _file1.txt_. He should run the following command to *pull* changes.
```bash
$ git pull
```
You will obviously also need to run this command if a colleague pushed changes that you want to pull.

## Solving merge conflicts
At this point, your branch is up to date with the remote repository and there is only one file in our repository, _file1.txt_, which contains
```bash
$ more file1.txt
> Toto goes to the beach
```
Now imagine that you locally modified _file1.txt_, *added* and *commited* it such that it contains
```bash
$ more file1.txt
> Toto goes to the mall
```

But one hour ago, your colleague John decided to add one line to _file1.txt_, *add*, *commit* and *push* this change, such that this file on the remote repository (on on John's local repo) contains
```bash
(John) $ more file1.txt
> Toto goes to the beach
> Tata goes to the bakery
```

So, if you try to *push* your local changes you should get the following message
```bash
$ git push
> To github.com:ngazagna/example.git
>  ! [rejected]        master -> master (non-fast-forward)
> error: failed to push some refs to 'git@github.com:ngazagna/example.git'
> hint: Updates were rejected because the tip of your current branch is behind
> hint: its remote counterpart. Integrate the remote changes (e.g.
> hint: 'git pull ...') before pushing again.
> hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
Ouch! There is a problem... Let's look at the output of `git status`
```bash
$ git status
> On branch master
> Your branch and 'origin/master' have diverged,
> and have 1 and 1 different commits each, respectively.
>   (use "git pull" to merge the remote branch into yours)
>
> nothing to commit, working tree clean
```

You first need to *pull* remote changes before *pushing*
```bash
$ git pull
> Auto-merging file1.txt
> CONFLICT (content): Merge conflict in file1.txt
> Automatic merge failed; fix conflicts and then commit the result.
```

Embarrassing... But don't worry! You just need to open _file1.txt_ in your favorite text editor (e.g., gedit). This is what you should see:
```bash
$ more file1.txt
> <<<<<<< HEAD
> Toto goes to the mall
> =======
> Toto goes to the beach
> Tata goes to the bakery
> >>>>>> 9ba0afaf0283c2b125899cbbd05bb7176da33ef6
```
And then, decide which version you prefer to keep. Let's say for instance that you edit _file1.txt_ the following way
```bash
$ more file1.txt
> Toto goes to the mall
> Tata goes to the bakery
```

Now you can *add*, *commit* and *push* your changes as usual
```bash
$ git add file1.txt

$ git commit -m "merging John update and mine"
> [master aa77bbf] merging John update and mine

$ git status
> On branch master
> Your branch is ahead of 'origin/master' by 2 commits.
>   (use "git push" to publish your local commits)
>
> nothing to commit, working tree clean

$ git push
> Counting objects: 6, done.
> Delta compression using up to 8 threads.
> Compressing objects: 100% (2/2), done.
> Writing objects: 100% (6/6), 568 bytes | 568.00 KiB/s, done.
> Total 6 (delta 0), reused 0 (delta 0)
> To github.com:ngazagna/example.git
>    9ba0afa..aa77bbf  master -> master
```

*Remark1*: if the conflict file has not been modified by the `git pull` command such that the markers  `<<<<<<< HEAD`, `=======` and `>>>>>>>` do not appear (and if nothing has been commited), try to do the following to retry another merge.
```bash
$ git merge --abort
$ git pull
```

*Remark2*: instead of `git pull` you can also use `git fetch` then `git diff master origin/master` to see what are the differences. It does not change you local file.

## Going back to a certain commit
If you prefer the original sentence you wrote. You can come back to the corresponding commit. Let's have a look at previous commit.
```bash
$ git reflog
> aa77bbf (HEAD -> master) HEAD@{0}: commit (merge): merging John update and mine
> 5feb06a HEAD@{1}: commit: beach -> mall
> 1b3899a HEAD@{2}: pull: Fast-forward
> b3b2b99 HEAD@{3}: reset: moving to HEAD
> b3b2b99 HEAD@{4}: commit (initial): new file created
```
So, if you want to definitively come back to the version corresponding to commit `b3b2b99` just perform the following command
```bash
$ git reset --hard b3b2b99
```
*Warning*: this will destroy all your local changes, so don't do it if you have uncommited changes that you want to keep.

See [this post](https://stackoverflow.com/questions/4114095/how-to-revert-a-git-repository-to-a-previous-commit) for more details.

## Ignoring files
When coding, you might have some auxillary files automatically generated when compiling (e.g., _.aux_ files in LaTeX). If you don't want to share/*track* these files with your co-workers, so you should tell `Git` to *ignore* them. Imagine you want to ignore a file _temp.aux_, you should create a `.gitignore` file in your repository (if it does not already exists) containing its name.
```bash
$ echo "temp.aux" > .gitignore
```
- You can also ignore files in subfolders by precising the path (starting from your working directory) of the files you want to ignore.
- You can ignore all the _.aux_ files by adding the line `*.aux` to you `.gitignore` file.
- You can add exceptions by placing an `!` before the name of file. For instance if you want to track _file_to_track.aux_ but not the other _.aux_ files, you `.gitignore` should contain
```
*.aux
!file_to_track.aux
```

## Some useful commands
- show current HEAD and its ancestry: `git log`
- show an ordered list of commits that HEAD has pointed to: `git reflog`
- git add several modified files that are tracked (see comment on `.gitignore`): `git add .`
- undo `git add <file>` before commit -> `git reset <file>`