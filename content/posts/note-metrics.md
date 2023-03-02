+++
title = "Cleaning The Castle"
date = "2022-11-08"
author = "Mark"
cover = "/img/oliver-hale-oTvU7Zmteic-unsplash.jpg"
tags = ["obsidian", "madness", "zettelkasten", "telegram"]
keywords = ["obsidian", "madness", "zettelkasten", "telegram"]
description = "Warbling into madness slowly."
showFullContent = false
draft = true
+++

# Howdy! 👋

A fine thing happened recently: Obisidian v1.0.0 was released. Which I discovered
through this hacker news post. The comment section on Hacker News are usually 💰 
and this one was no exception, here is a quote from the top comment:

```
Finally, a note taking application with a decent API that's allowed me to extract metadata and publish metrics into CloudWatch, allowing me to track key metrics and graphically[0] review historical trends of my "second brain." Previous note taking applications I've tried in the past (e.g. Zettlr, Bear) lacked the vibrant developer community that Obsidian has cultivated.

```

👆 Posts like this keep me coming back to Hacker News. Also, game recognises game. Here's
a link their [blog post](https://digitalorganizationdad.substack.com/p/stop-zettelkasten-literature-notes) explaining the full stack and pipeline.

I bet you are sat there saying what if we could do something similar, but using 
only self hosted tools?

Well you, friend, are in luck.

## Beginning the yak shaving

Here is the flow I came up with for this experiment.

![flow](/img/flow.png)

My daily template in Obsidian autopopulates the `#unprocessed` tag. In theory these are
processed daily, however, in practise that frequently doesn't happen.

This is the stack:

Tech|Use|
-----|-----|
Golang|Used to create the prometheus exporter|
Prometheus|Used to track note metrics|
Alert Manager|For handling alerts|
Telegram|To receive notifications|
Grafana|For visualising note metrics|

First things first, we need a program which can expose the unprocessed
notes as a prometheus compatible metric.

## Consuming the data

I already have Prometheus, Alert Manager and Grafana running in my network, however, 
the exporter directory contains a docker-compose example in case you want to get up and
running quickly.

## Next Steps

The exporter could be extended to show more metrics from your vault.