# Git Cheatsheet

## Help

!!! tip

    As with all the commands, this is just meant to save time. **Every** command
    can be learned more in depth using either `man`, `git help <command>`, or `--help` where available. Below is git syntax.

!!! example "Show general help"

    ```shell
    git help
    ```

!!! example "Open the manual for a command"

    ```shell
    git help <command>
    ```

!!! example "Show abbreviated command help"

    ```shell
    git <command> --help
    ```

---

## Quick Reference

| Command | Description |
| - | - |
| `git status` | Check repository state |
| `git diff` | View unstaged changes |
| `git diff --staged` | View staged changes |
| `git add <file>` | Stage one file |
| `git add --all` | Stage all changes |
| `git commit -m "message"` | Commit staged changes |
| `git fetch` | Download remote references |
| `git pull` | Pull tracked branch |
| `git push` | Push tracked branch |
| `git switch -c <branch>` | Create and switch branches |
| `git switch <branch>` | Switch branches |
| `git merge <branch>` | Merge a branch |
| `git restore --staged <file>` | Unstage a file |
| `git restore <file>` | Discard unstaged changes |
| `git stash` | Temporarily save changes |
| `git log --oneline` | Show compact history |
| `git reflog` | Recover earlier references |

---

## Git Status

Common alterations to stock `git status` command.

| Command | Description |
| - | - |
| `--short` | Compact format |
| `--branch` | Show the branch tracking |
| `--show-stash` | Show the number of stashed entries |
| `--short` | Long format (default) |
| `--verbose` | In addition to names of files, shows contextual changes - similar to **git dif --cached** |
| `--untracked-files` | Show untracked files, defaults to **all**. Possible options: |

---

## Repository Setup

### Create a repository

!!! example

    ```shell
    mkdir hello-world
    cd hello-world
    git init
    ```

### Clone an existing repository

!!! example "General Usage"

    ```shell
    git clone <repository-url>
    ```

!!! example "Clone into a specific directory"

    ```shell
    git clone <repository-url> <directory>
    ```

### View repository configuration

!!! example "List all configs"

    ```shell
    git config --list
    ```

!!! example "List local configs"

    ```shell
    git config --local --list
    ```

### Configure identity

!!! example

    ```shell
    git config --global user.name "Your Name"
    git config --global user.email "your-email@example.com"
    ```

!!! example "View configured identity"

    ```shell
    git config --global user.name
    git config --global user.email
    ```

---

## Standard workflow

!!! example

    Check the repository:

    ```shell
    git status
    ```

    Review changes:

    ```shell
    git diff
    ```

    Stage changes:

    ```shell
    git add .
    ```

    Review the staged changes:

    ```shell
    git diff --staged
    ```

    Commit:

    ```shell
    git commit -m "Describe the change"
    ```

    Pull remote changes:

    ```shell
    git pull
    ```

    Push local commits:

    ```shell
    git push
    ```

---

## Inspect Changes

| Command | Description |
| - | - |
| `git diff` | Show unstaged changes |
| `git diff --staged` | Show staged changes |
| `git diff HEAD` | Show all staged and unstaged changes since the latest commit |
| `git diff <branch-a>..<branch-b>` | Compare two branches |
| `git diff <commit-a>..<commit-b>` | Compare two commits |
| `git show <commit>` | Show a commit and its patch |
| `git blame <file>` | Show which commit last changed each line of a file |

!!! example "Show changes to one file"

    ```shell
    git diff -- path/to/file
    ```

!!! example "Show staged changes to one file"

    ```shell
    git diff --staged -- path/to/file
    ```

---

## Staging Changes

!!! example "Stage one file"

    ```shell
    git add <file>
    ```

!!! example "Stage multiple files"

    ```shell
    git add <file-one> <file-two>
    ```

!!! example "Stage all changes under current directory"

    ```shell
    git add .
    ```

!!! example "Stage all tracked and untracked changes"

    ```shell
    git add --all
    ```

!!! example "Interactively choose changes"

    ```shell
    git add --patch
    ```
!!! example "Remove a file from staging area"

    ```shell
    git restore --staged <file>
    ```

---

## Commits

!!! example "Create a commit"

    ```shell
    git commit -m "Describe the change"
    ```

!!! example "Open the configured editor to write a longer commit message"

    ```shell
    git commit
    ```

!!! example "Amend the most recent commit"

    ```shell
    git commit --amend
    ```

!!! example "Amend without changing its message"

    ```shell
    git commit --amend --no-edit
    ```

    !!! danger

        Amending replaces the previous commit. Avoid amending a commit that has already been pushed and shared unless rewriting the branch is intentional.

---

## Branches

### View branches

!!! example "Show local branches"

    ```shell
    git branch
    ```

!!! example "Show local and remote branches"

    ```shell
    git branch --all
    ```

!!! example "Show each branch and its latest commit"

    ```shell
    git branch --verbose
    ```

### Create branches

!!! example

    ```shell
    git branch <branch-name>
    ```

!!! example "Create and switch to a new branch"

    ```shell
    git switch --create <branch-name>
    ```
    -or-

    ```shell
    git checkout -c <branch-name>
    ```

    -or-

    ```shell
    git checkout -b <branch-name>
    ```

### Switch branches

!!! example

    ```shell
    git switch <branch-name>
    ```

    -or-

    ```shell
    git checkout <branch-name>
    ```

!!! example "Return to the previous branch"

    ```shell
    git switch -
    ```

### Rename a branch

!!! example "Rename the current branch"

    ```shell
    git branch --move <new-name>
    ```

!!! example "Rename a specific branch"

    ```shell
    git branch --move <old-name> <new-name>
    ```

### Delete branches

!!! example "Delete a merged local branch"

    ```shell
    git branch --delete <branch-name>
    ```

    -or-

    ```shell
    git branch -d <branch-name>
    ```

!!! example "Force delete a local branch"

    ```shell
    git branch --delete --force <branch-name>
    ```

    -or-

    ```shell
    git branch -D <branch-name>
    ```

!!! example "Delete a remote branch"

    ```shell
    git push origin --delete <branch-name>
    ```

---

## Merging

!!! example "How to merge"

    Checkout the branch that should receive the changes:

    ```shell
    git checkout main
    ```

    Merge another branch into the current branch:

    ```shell
    git merge <branch-name>
    ```

    !!! example

        ```shell
        git checkout main
        git merge feature/fix-bug123
        ```

!!! example "Abort a merge in progress"

    ```shell
    git merge --abort
    ```

    After resolving merge conflicts:

    ```shell
    git add <resolved-file>
    git commit
    ```

---

## Remotes

### View Remotes

### Add a remote

### Change a remote URL

### Remove a remote

---

## Fetch, Pull, and Push

### Fetch

### Pull

### Push

---

## Undo Changes

### Discard unstaged changes to one file

### Discard all unstaged changes

### Unstage a file

### Revert a commit safely

### Reset the latest local commit

---

## Stashing

---

## Commit History

---

## Tags

---

## Remove and Rename Files

---

## '.gitignore'

---

## Common Recovery Commands

Show the movement of branches and `HEAD`:

```shell
git reflog
```

Restore a lost commit by creating a branch from it:

```shell
git branch recovery/<name> <commit>
```

Find the common ancestor of two branches:

```shell
git merge-base <branch-a> <branch-b>
```

Check repository object integrity:

```shell
git fsck
```

---
