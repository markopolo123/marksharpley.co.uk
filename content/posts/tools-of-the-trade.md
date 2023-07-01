+++
title = "Tools of The Trade :: Git"
date = "2021-12-18"
author = "Mark"
featuredImage = "/img/tools.jpg"
tags = ["git", "tools-of-the-trade", "git-ops", ]
keywords = ["git", "tools-of-the-trade", "git-ops"]
description = "First post in a series looking at tooling which makes work easier"
showFullContent = false
draft = false
+++

# Introduction

This is the first in a series of posts about tools and processes which make my
life as a engineer better. In this first post we'll take a look at `Git` .

[Git](https://git-scm.com) is a wonderful tool. For teams working together (and
individuals!) on source code, `git` is the go to for distributed version control
software.

> It's assumed here that _the reader_ already has a working knowledge of Git.
> This post focuses on tooling to be used _on top_ of Git

## Code Quality

Git supports `hooks` which may be used to trigger actions when certain events
occur, both on your local client and on the server. [This
page](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) shows more
details on them. I used to write my own hooks, until I discovered
[pre-commit](https://pre-commit.com). This is a python based tool for managing
your git hooks. It's easy to write your own and there is a large library of
hooks already out there on the Internet.

I use this on my client device and also in my CI to ensure that code pushed
meets set expectations.

Some of the many, many things which pre-commit can do for you:

* Ensure that ansible vault files are encrypted
* Stop commiting of private keys
* Linting and style checks.

## Standardised Commit Messages

Standardising commit messages is a bit more `out there` , but it makes sense for
a few reasons detailed below. There are a few options in this space but I prefer
[Commitizen](https://commitizen-tools.github.io/commitizen/).

> Somewhat confusingly there are **two** projects called `Commitizen` . Both are
> great but share only a name. The one above is based on `python` , the [other
> (cz-js)](https://github.com/commitizen/cz-cli) is based on `Javascript`

### Reduce Friction for contributers and maintainers

Providing templates and running git hooks helps with commit message quality, but
provides slow feedback for **individual contributors**. Using `Commitizen`

aleviates this.

### You can use it to trigger CI

When combined with pre-commit (to check your commit messages conform to your
standard), users gain the ability to start creating `#git-ops` pipelines which
may take actions based on the contents of commit messages. Some obvious wins
here:

* Workflows for tagging and releasing code based on `semantic versioning`
* Automated building of changelogs

While `Commitizen` is very useful out of the box it's also dead easy to extend
it and customise.

## Git GUI

There are many tools for providing a `Graphical User Interface` ontop of git.
VSCode, which I use heavily, has many extensions worth talking about.

Out of all of these, there is one application which provides **joy** every time
I use it:

### Lazygit

[LazyGit](https://github.com/jesseduffield/lazygit) is written for users which
want a Git GUI _in their terminal_. It reduces the friction and learning curve
on doing more complex (and simple) Git operations.

![lazygit in action](/img/lazygit.png)

It is
[customisable](https://github.com/jesseduffield/lazygit/wiki/Custom-Commands-Compendium)
and works well with `Commitizen`

## Standardised git flow

While the above tools are useful, having a standardised git flow across your is
much more important. Especially if you are in a team working across git
repositories.

Have this documented as a living document alongside your team's coding standards
is a good practice.

## Cloning many organisation repos from GitHub

This is most easily accomplished using the Github CLI.

If using homebrew on a mac instsall the Github CLI:

```bash
brew install gh
```

```bash
# Login with CLI tool
gh auth login

# Set your orgname
$myorgname=yourorgname
# Clone up to 1000 repos under your current folder
gh repo list $myorgname --limit 1000 | while read -r repo _; do
  gh repo clone "$repo" "$repo"
done
```
