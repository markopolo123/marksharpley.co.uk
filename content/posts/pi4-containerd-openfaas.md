+++
title = "OpenFAAS via Containerd on a Raspberry PI 4"
date = "2019-12-20"
author = "Mark"
cover = "/img/pi4-openfaas-containerd.jpg"
tags = ["OpenFAAS", "Pi4", "Containerd"]
keywords = ["serverless", "Linux"]
description = "Testing OpenFAAS with Containerd on a Raspberry PI 4"
showFullContent = false
+++


# Intro
[OpenFAAS](https://www.openfaas.com/) is a serverless tool. One of the things I like most about it - it's pretty agnostic. You can bring your own infra or orchestrator and roll with Dockerfiles. No vendor lock in!

Their tagline:
`OpenFaaS® makes it simple to deploy both functions and existing code to Kubernetes`

While it is mainly used with k8s, there are also providers for other orchestrators, such as Hashicorp's Nomad. Today I'll be trying out a new provider for [Containerd](https://containerd.io/) on my Raspberry Pi 4b.

# Installation

Before we can install OpenFAAS a few dependancies are required. 

## Dependancies
```bash
sudo apt update && \
  sudo apt install -qy golang runc bridge-utils ethtool tmux git
```


## Install Containerd for Stretch

Getting Containerd running requried me to use the Debian Buster packages...
```bash
wget https://download.docker.com/linux/debian/dists/buster/pool/stable/armhf/containerd.io_1.2.6-3_armhf.deb
sudo dpkg -i containerd.io_1.2.6-3_armhf.deb

# Check containerd is running
sudo systemctl status containerd
```
## Enable Forwarding

```bash
sudo /sbin/sysctl -w net.ipv4.conf.all.forwarding=1
```

## Install a bunch of Go Packages

```bash
export GOPATH=$HOME/go/

go get -u github.com/genuinetools/netns
sudo mv $GOPATH/bin/netns /usr/bin/
# Grab and build openfaas for containerd
mkdir -p $GOPATH/src/github.com/alexellis/faas-containerd
cd $GOPATH/src/github.com/alexellis/faas-containerd
git clone https://github.com/alexellis/faas-containerd
cd faas-containerd
go build && sudo function_uptime=120m ./faas-containerd
```

At this point OpenFaas should be listening on http://127.0.0.1:8081.

We may test this using the `faas-cli` tool...


```bash
# Grabbing the faas-cli tool
curl -sLfS https://cli.openfaas.com | sudo sh

# Deploying a function
faas store deploy figlet -g 127.0.0.1:8081 --update=true --replace=false

# Lets see if figlet's okay
faas-cli describe figlet -g 127.0.0.1:8081

# Showing running containers
sudo ctr --namespace openfaas-fn containers list

# Testing the function
curl -X POST http://127.0.0.1:8081/function/figlet -d 'openfaas on containerd'
```
![OpenFaas on a Raspberry Pi 4 using Containerd](/img/openfaas.png)