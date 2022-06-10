+++
title = "New Hugo Site"
date = "2019-12-11"
author = "Mark"
cover = ""
tags = ["pipeline", "hugo"]
keywords = ["", ""]
description = "Setting up a new static site"
showFullContent = false
+++

First post on the new site. It's a [hugo](https://gohugo.io/) site, using the terminal theme by [panr](https://twitter.com/panr). Previously Pelican was my static site generator of choice but I've heard good things about Hugo and fancied giving it a go.

Here's how its configured:

# Installation

I'm using Hugo with [Github Pages](https://gohugo.io/hosting-and-deployment/hosting-on-github/)) and my own hosting. 

As I won't be using Hugo/this blog everyday I will forget the commands for it so I've wrapped everything I need into a Makefile:


```makefile

help:
	@echo "make {build|test|deploy|new-post}"
	@echo "make new-post POST=test"
	
build:
	@hugo -t terminal

test:
	@hugo server -t terminal

deploy: build
	./scripts/deploy-gh.sh
	./scripts/deploy-sftp.sh

new-post:
	hugo new posts/$(POST).md
	code .
```

The site source code is available [here](https://github.com/markopolo123/site-source).

# Why?

Few reasons: 
* I want to practise and hopefully improve my writing
* [Drew Devault's blog post](https://drewdevault.com/make-a-blog)