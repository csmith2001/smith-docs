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

- Use `git add <path-to-file>` to stage files for commits. (use `.` to add all files from current working directory)
- Use `git commit` to commit staged files. (Add `-m` to add an in-line commit message)

---

## Git Log

A git repo is a list of commits, and each commit represents the
*full state of the repository* at a given point in time.

The `git log` command shows a history of the commits in a repo. Here
you will see a commit's author, hash, and date.

### A commit hash

Each commit has a unique identifier called a "commit hash". This is a long string
of characters that uniquely identifies the commit. For convenience, you can refer
to any commit by using the first `7` characters.

A hash is a SHA-1 cryptographic hash.

### Usage

General syntax is as follows:

```shell
git log [<options>] [<revision-range>] [[--] <path>...]
```

Some common options used are:

- `git log --oneline`
- `git log --no-pager`
- `git log -n <Number>`

For an extensive list of options:

```shell
git log --help
```

Or:

```shell
man git log
```

### Git Objects

All the data in git, commits, branches, tags, etc. are referred to as '`objects`'.

Objects are stored in the `.git/objects` directory.

Commits are a type of object that can be viewed within this directory using `cd` and `git cat-file`:

```shell
cd .git/objects/<first-two-sha-chars> &&
git cat-file -p <commit-hash>
```

A `tree` is git's way of storing a directory. Notice however, we cannot see the contents of files.

Notice that we can see:

- The `tree` object
- The `author`
- The `committer`
- The commit message

This is because the `blob` object is what stores this:

- A `blob` is git's way of storing a file, a.k.a. binary large object storage.

If you run `git cat-file` on the hash of the `tree` object, you should now see
the `blob` objects in this commit with their own hashes.
