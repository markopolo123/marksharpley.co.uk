+++
title = "Why I'm now using 1Password"
date = "2022-06-22"
author = "Mark"
cover = "/img/keys.jpg"
tags = ["1password", "secrets"]
keywords = ["1password", "secrets"]
description = "Ravings about 1password"
showFullContent = false
draft = false
+++

# Howdy 👋

I recently purchased an *idevice*. "Fantastic", I hear you say, but what's that
got to do with 1password? 

For years I've been an ardent keepassxc user, syncing database files between devices
using syncthing or similar self hosted tools and annoying everyone with the warm glow
of smug self reliance.
Sadly, Idevices don't like that kind of thing. It's iCloud or the highway for syncing files.
Not wanting to iCloud all the things I decided to check out the big bad world of
cloud hosted secrets management.

> Spoiler, I put nearly all my secrets in the cloud ☁️

While searching online it quickly became obvious that there are two big players, 1password
and bitwarden. Bitwarden has options for self hosting, but 1password came with a glowing
review from my mate.

I decided to give 1password some money based on the following features:

* Everything their side is encrypted, with keys supplied by the client
* Great reputation across the industry
* CLI tools for accessing secrets
* RBAC and granular sharing capabilities
* Applications for every operating system I use
* Browser extensions for firefox and chrome
* Easy export capability

I started testing the water, moving a few things from keepassxc into 1password.
A few days later I threw down money for a 1password subscription. Here's why:

### SSH Key Management 🔑

> this ones a bit of a game changer...

1Password has an [SSH Agent built in](https://developer.1password.com/docs/ssh/get-started)
and can manage SSH keys.
The docs are easy to use and clear. 

#### Some things I found when setting this up

Use the nightly version of the 1Password application. If you don't sessions won't persist
and you will be requested for auth on each connection. Not cool if your Terraform or
Ansible are using SSH a lot.

Set SSH_AUTH_SOCK variable in your rc file or similar:
```bash
# without SSH_AUTH_SOCK being set you won't be able to list keys using ssh-add -l
# Note the path is for MacOS only
export SSH_AUTH_SOCK=~/Library/Group\ Containers/2BUA8C4S2C.com.1password/t/agent.sock
```

This has changed my workflows. I no longer have keys per device. Instead, keys exist per
service and *context*.

### Secrets Automation 🤖

Taking 1Password a step towards Hashicorp Vault,
[ secrets automation](https://developer.1password.com/docs/connect/) gives your
infra the ability to consume a private REST API.
### Fastmail Integration 📧

1password has [worked with fastmail](https://1password.com/fastmail/) to create
automatic unique email addresses per account.

This is pretty cool!

### CLI Config File Rendering 🏗️

I mostly use direnv and the 1password cli, however, it's nice to have [config rendering](https://developer.1password.com/docs/cli/secrets-config-files) available.

```bash
database:
    host: http://localhost
    port: 5432
    username: op://prod/mysql/username
    password: op://prod/mysql/password
```
### Item Templates 🍪

If you need to create custom templates for new entries in your vault
[1Password has you covered](https://developer.1password.com/docs/cli/item-template-json).

## Travel Mode

[Travel Mode](https://support.1password.com/travel-mode/)
makes certain vaults unavailable when you are travelling. Won't protect you from Mossad,
but will give you piece of mind in normal travel situations

![obligatory xkcd](https://imgs.xkcd.com/comics/security.png)

## Summary

Two months of using this service and I keep finding useful features. It's well worth the
money!