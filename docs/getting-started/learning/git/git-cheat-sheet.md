---
icon:
---

# Git in 5min

New to git? No worry, let's make a quick-and-dirty introduction
and make you dangerous!

---

## What is git

Git has been around for a long time, and until recent events seen with Scion (Anthropic)
has been (for many still is) *the* distributed version control system (VCS). For as long
as the industry has been the way it was, nearly every developer in the world uses it to
manage their code.

Git was made by the legendary Linus Torvalds who also created Linux.

It is used to:

- Keep a history of code changes
- Revert mistakes made
- Collaborate with other developers
- Make backups of code
- Much more

Git can be summarized into [two types](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain) of commands being ran:

1. High-level "***Porcelain***" commands
2. Low-Level "***Plumbing***" commands

### Porcelain Commands

Examples:

- `git status`
- `git add`
- `git commit`
- `git push`
- `git pull`
- `git log`

### Plumbing Commands

Examples:

- `git apply`
- `git commit-tree`
- `git hash-object`
- `ls-files -s`
- `update-index`
- `update-ref`

---

## Create a repo

We need to create a project to work with git.

To do so:

```shell
mkdir ~/some-project &&
cd ~/some-project
```

Initialize a repository:

```shell
git init
```

You should now have a hidden `.git` directory in your directory.

---

## Configuration

We need to configure git to contain our information. Whenever code changes,
git tracks who made the change. To ensure who gets the credited work done,
you need to set your name and email.

### Local

At the project level (`./`), you have a hidden `.git` folder.

Inside, you can edit the contents by:

```shell
cd ~/some-project &&
git config set pull.rebase=false
```

### Global

You also set configurations at the global level using `--global`, for example, your name and email:

```shell
git config set --global user.name "username" &&
git config ser --global user.email "email@example.com"
```

---

## Status

A file in a repository can be in one of several states:

- `Untracked`
- `Unmodified (Committed)`
- `Modified`
- `Staged`

The `git status` command shows you the current state of your repo.
