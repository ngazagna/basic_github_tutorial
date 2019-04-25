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

*Remark*: if the conflict file has not been modified by the `git pull` command such that the markers  `<<<<<<< HEAD`, `=======` and `>>>>>>>` appear (and if nothing has been commited), try to do the following to retry another merge.
```bash
$ git merge --abort
$ git pull
```




## Some useful things to know

undo `git add` before commit -> `git reset <file>`