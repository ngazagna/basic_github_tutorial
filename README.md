# basic_github_tutorial
Basic tutorial on git + hosting service (e.g. Github) for people collaborating on a shared project.

## What is pull and push?
*Remark*: I encourage you to run the `git status` command at every step of this tutorial to better understand what git does.

Imagine you decide to *create* a new file called _file1.txt_ and you want to share it with colleagues. You should first create it:
```bash
$ echo "Toto goes to the beach" > file1.txt
```
Then, you would like to *add* the file to the index of git and then to *commit* this change, which means to record changes to the repository.
```bash
$ git add file1.txt
$ git status
> On branch master
> Your branch is up to date with 'origin/master'.
>
> Untracked files:
>   (use "git add <file>..." to include in what will be committed)
>
> 	 file1.txt
>
> nothing added to commit but untracked files present (use "git add" to track)
$ git commit -m "new file created"
>
```
Note that you should always try to write clear and concise commit messages (after the `-m` argument) in order to make life easier for you co-workers.

Now, your changes have been recorded locally, but you want to share them with your colleagues. So, let's *push* them on the repository hosted online.
```bash
$ git push
```

Finally, imagine that one of your colleagues wants to get access to _file1.txt_. He should run the following command to *pull* changes.
```bash
$ git pull
```
You will obviously also need to run this command if a colleague pushed changes that you want to pull.

## Managing merge conflicts
At this point, there is only one file in our repository, _file1.txt_, which contains
```bash
$ more file1.txt
> Toto goes to the beach
```





## Some useful things to know

undo `git add` before commit -> `git reset <file>`