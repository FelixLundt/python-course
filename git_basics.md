By now, you should have the project set up with the correct layout, and an implementation of the core Connect Four functions (plus their associated tests). The next step is to get those projects into a git repository and a remote repository on GitHub. There are a few ways to achieve this, but I recommend to stick to [this guide](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github).

Note the hint to create an empty repository on GitHub, without `README.md` and`.gitignore` files and license to avoid errors. However, I recommend adding those after you've pushed your local repo to GitHub for the first time.

## Git for students using Windows

If you're using Linux or MacOS, skip this section since those operating systems don't require any special set-up for git.

There are a couple of options for using git on Windows. The first is to use [Git for windows](https://gitforwindows.org/), which includes Git BASH, a linux shell emulator. The second option is to use [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/faq), and install bash & git (though this option may require a more complicated set-up, see [here](https://medium.com/faun/how-to-use-git-and-other-linux-tools-in-wsl-on-windows-4c0bffb68b35)).


## First steps with Git

Now that we have our project under version control, let's see what git's view of the working directory is. Inside the repository working dir, run `git status`. You should see something like:

```
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
    
    agents/
    tests/
    game_utils.py
    main.py
```

If you're on Mac OS, you'll also probably have a file `.DS_Store` listed. Things like special files created by your IDE (e.g., `.idea` related to a PyCharm project) should not go in the repository, so we want to avoid accidentally adding them. This is achieved by using the `.gitignore` file, which you'll be shocked to learn, tells git to... ignore files (that match certain patterns).

If this is the case, in the `.gitignore` file you can add the lines:

```
# PyCharm project settings
.idea/
# Mac folder attributes file
.DS_Store
```

Now run `git status` again. It should now list only the `.gitignore` file, `agents` directory, and `main.py`. There should be no mention of `.idea` or `.DS_Store`, because git is now ignoring it, as per your request.

### Staging changes for commit

We can finally tell git to start tracking our program source. Since we want to add all of the untracked files to the repository, we can use `git add .` (the period indicates the current directory, so it will add all untracked files recursively starting here). If you have untracked files you don't want to add, and for some reason can't add them to the `.gitignore` file, then you can specify which files to add with `git add <path specifier>` (you can specify multiple files or paths).

Run `git status` again. You should see something like:

```
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    modified:   .gitignore
    new file:   agents/__init__.py
    new file:   agents/common.py
    new file:   agents/agent_minimax/__init__.py
    new file:   agents/agent_minimax/minimax.py
    new file:   agents/tests/__init__.py
    new file:   tests/test_game_utils.py
    new file:   game_utils.py
    new file:   main.py
```

Running `git add` does not put (new or changed) files into the repository. It merely "stages" (prepares) them to be committed to the repository. This staging is useful because it allows you to compose/design a commit before actually performing it, which is especially helpful for larger commits.

### Committing changes to the local repo

We've now staged the Python source files (and the modified `.gitignore`), it's time commit them to the repository using `git commit -m "<some informative message>"`. After committing, run `git status` and you should see something like:

```
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

### Pushing changes to the remote repo

There are two things of note in the output from the previous section. First, your working directory ("working tree") and your local repo are "synchronized", i.e. there are no changes in your working directory missing from the local repo. Two, your local repo has a commit that is not in the GitHub copy of the repo ("origin/main", where "origin" refers to the remote repository from which this repo was cloned, and "main" refers to the branch name). To send the changes from your local repo to the remote GitHub repo, run `git push` (`git push -u origin main` might be necessary).

Congratulations, you've just published your code on GitHub!

### Added a file by mistake?

You can untrack files by running `git rm --cached <filename>` or `git rm -r --cached <dirname>`, and then commiting the changes. This will not delete your local copy, but might lead to deletion of those files for other users that pull the commit from your repository. So only do this if no one has cloned your repo yet, or if you don't care that it deletes the file for other users.

## Further reading on Git

The basic git workflow is pretty straightforward: Make changes in your working directory, stage those changes (`git add <filename>`), commit them to your local repo (`git commit -m "<useful message>"`), push them to the remote (`git push`). Unfortunately, things can get more complicated, especially when collaborating with others.

I highly recommend you read the git book, [found here](https://git-scm.com/book). In particular, I would encourage you to read [Chapter 10. Git Internals](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain) and actually run the commands they use in their examples. Having a basic familiarity with the internals of git is fantastically illuminating and will help you understand *why* certain git commands are necessary, and what they're doing under the covers.

### Line endings and Git

If you're working on Windows, or you store your code on an NTFS (Windows) partition, be sure to read [this](https://help.github.com/en/github/using-git/configuring-git-to-handle-line-endings).