+++
title = "Restic Backups to backblaze"
date = "2020-06-06"
author = "Mark"
authorTwitter = "" #do not include @
cover = "/img/cut-drive.jpg"
tags = ["restic", "linux", "backups", "backblaze"]
keywords = ["restic", "linux", "backblaze", "backups"]
description = "Using Restic to backup to backblaze B2"
showFullContent = false
draft = true
+++

[Restic](https://restic.net) is a nifty backup tool written using
[GOLANG](https://golang.org/). It can encrypt backups, do deduplication and is
compatible with all common cloud provider's storage offerings. Here we will be
backing up super important data to [BackBlaze
B2](https://www.backblaze.com/b2/cloud-storage.html).

[BackBlaze B2 Cloud Storage](https://www.backblaze.com/b2/docs/) is pretty
great. the company are open and up front [about their
costs](https://www.backblaze.com/b2/cloud-storage-pricing.html) and publish a
regular [report on drive stats](https://www.backblaze.com/blog/). The idea of
using backblaze here for me is simple - I already back up locally but want to
make sure that my data is also stored safely off site. I don't ever intend to
have to download it from backblaze but it's nice to know it's there.

### Installing Restic

The Restic maintainers offer a [binary
release](https://github.com/restic/restic/releases) from their repository. It's
also available to install in Ubuntu using package managers if that's your thing.

## Installing using APT

On Ubuntu Restic is available using apt, commonly a version or two behind the
current release.

From your terminal:

```bash
# Find restic version in apt
apt search restic

# Install restic using apt
sudo apt install restic
```

## Installing using SNAP

If you are a snap fan you may install the [snap
package](https://snapcraft.io/restic):

{{< rawhtml >}}

<iframe src="https://snapcraft.io/restic/embedded?button=black" frameborder="0" width="100%" height="300px" style="border: 1px solid #CCC; border-radius: 2px; "></iframe>
{{< /rawhtml >}}

# Preparing a Repository

Restic calls backups `repositories` .
