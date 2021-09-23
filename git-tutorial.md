# 1. Git Tutorial for Bitcoin Core

- [1.1 Basic Operations](#1)
- [1.2 Squashing and Rebasing](#2)
- [1.3 Writing Good Commit messages](#3)
- [1.4 Creating Proper PR Titles](#4)
- [1.5 Sources](#4)

## 1.1 Basic Operations

### 1.1.1 Fork the Project

Go to `https://github.com/bitcoin/bitcoin` and click on the `Fork` button in the upper right corner.

This will create the `<username>/bitcoin` project.

### 1.1.2 Clone the Project

To clone the forked project, the command below can be used. This will allow you to work on the code and to open pull requests.

```bash
git clone https://github.com/<username>/bitcoin.git [<directory>]
```

The `directory` argument is optional. If no one is provided, the name of the repository will be used (`bitcoin` in this case).

If the intention is just review or study the code, you can clone the original repository.

```bash
git clone https://github.com/bitcoin/bitcoin.git [<directory>]
```

### 1.1.3 Add Remote Repository

If you intend to open pull requests and contribute to original code, you also need to add the original repository.

Otherwise, this step can be skipped.

```bash
git remote add upstream https://github.com/bitcoin/bitcoin.git
```

### 1.1.4 Create a New Branch

To propose a new feature, a new branch must be used to develop it. This can be done with the command:

```bash
git checkout -b new_branch
Switched to a new branch ‘new_branch’
```

Specifying `-b` causes a new branch to be created as if `git-branch` were called and then checked out.

If switching to an existing branch, this parameter must be omitted.

### 1.1.5 Commiting and Publishing the Changes

After making the changes, you can check if the files that has been changed with `git status`. This command is used to examine the current state of the repository and can be used to confirm a git add promotion.

The commit can be done with the following commands:

```bash
git add .
git commit -m "First commit"
git push -u origin new_branch
```

The `git add` command adds a change in the working directory to the staging area. It tells Git that you want to include updates to a particular file in the next commit. However, git add doesn't really affect the repository in any significant way—changes are not actually recorded until you run `git commit`.

If you want to add specific files to the staging area, you can use `git add <filename>`.

The `git reset` command is used to undo a git add. The `git commit` command is then used to commit a snapshot of the staging directory to the repositories commit history.

You can also use `git commit -a -m "First commit"`.Specifying `-a` automatically stages all files that git already knows about.

The `git push` is essential for a complete collaborative Git workflow. It is utilized to send the committed changes to remote repositories for collaboration.

The `-u` argument sets up the association between the current branch and the remote one (defined in the step 1.1.3). You only need to use `-u` once.

Alternatively, you can use the `–set-upstream` option that is equivalent to the `-u` option.

### 1.1.6 Create the Pull Request in GitHub

Once you push the changes to the repository, the `Compare & pull request` button will appear in GitHub in the initial page.

Open a pull request by clicking this button. This allows the repository maintainers to review the contribution. From here, they can merge it if it is good, or they can request some changes.

### 1.1.7 Review a Pull Request

GitHub exposes PRs as branches on the upstream repository with `pull/<pr_number>/head`.  So they can be retrieved with following command:

```bash
git fetch origin pull/<pr_number>/head && git checkout FETCH_HEAD
```

The review message usually contains `ACK` if the reviewers agree with the changes or `NACK` if not.

An `ACK` is usually followed by a description of how the review was done.

`Concept ACK` means the reviewer agrees with the objective of the change, but has not looked at or tested the code.

`Approach ACK` means agreement with both the objective and the approach used in the change.

`Code review ACK` means the code has been reviewed but not yet tested.

`Tested ACK` (or `tACK`) means the code was tested and the reviewer agrees with the change.

Some reviews contain a concise description of the tests performed, such as the [PR 23065 - comment](https://github.com/bitcoin/bitcoin/pull/23065#issuecomment-925224499). It is good practice for new contributors.

When giving an `ACK`, specify the commits reviewed by appending the commit hash of the HEAD commit, for example, `tACK 94b6c8d`.

### 1.1.7 Get a Specific Version (Tag)

The command `git tag` shows all existing tags.

You can switch to a specific tag with the command. Useful when testing a version.

```bash
git checkout tags/v0.21.0
```

## 1.2 Squashing and Rebasing

Keeping commits organized is good practice. Usually after opening a PR, reviewers will make suggestions and the code will need to be changed. So, rather than adding new commits indiscriminately, `git rebase` can be used to organize these new commits in a coherent way.

### 1.2.1 Amending your last commit

If the `--amend` option is used, the changes will be squashed into the most recent commit.
This is the fastest way to merge two commits into one.

```bash
git commit -a --amend
```

### 1.2.2 Fixing up older commits

Amending only works for the most recent commit. But it's common to need to fix an older commit.

There is a tool to do this: the interactive rebase, which can be used in two ways: passing `HEAD~n`, where `n` is the last `n`  commits are about to be rebased. Or passing the commit hash as parameter.

```bash
git rebase -i HEAD~3
# or
git rebase -i 1be0581
```

This will open the default text editor with something like this:

```
pick 8d3fc77 Add test.cpp
pick 2a73a77 Change net.h
pick 0b9d0bb Add net.h

# Rebase f5f19fb..0b9d0bb onto f5f19fb (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# f, fixup <commit> = like "squash", but discard this commit's log message
```

When you run git rebase -i, you get an editor session listing all of the commits that are being rebased and a number of options for what you can do to them. The default choice is `pick`.

`pick` maintains the commit in your history.

`reword` allows you to change a commit message, perhaps to fix a typo or add additional commentary.

`squash` merges multiple commits into one.

`fixup` is like `squash`, but discard this commit's log message.

You can reorder commits by moving them around in the file.

Change from `pick` to `fixup` the commits that should be merged.

```
pick 8d3fc77 Add test.cpp
fixup 2a73a77 Change net.h
pick 0b9d0bb Add net.h
```

After saving and closing the text editor, git will remove all these commits from its history and then execute each line, one at a time. Lines with the `pick` command will be kept. Those with `fixup` or `squash` will be squashed.

### 1.2.3 Using git rebase --autosquash



## 1.5 Sources:

[How to create a pull request in GitHub](https://opensource.com/article/19/7/create-pull-request-github)

[How to Review Pull Requests in Bitcoin Core](https://jonatack.github.io/articles/how-to-review-pull-requests-in-bitcoin-core)

[Git Reference](https://git-scm.com/docs/)

[Saving Changes](https://www.atlassian.com/git/tutorials/saving-changes)