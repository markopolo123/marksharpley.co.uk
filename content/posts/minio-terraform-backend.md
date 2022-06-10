+++
title = "Using Minio as a backend for Terraform"
date = "2022-06-09"
author = "Mark"
cover = "/img/mars.jpg"
tags = ["Terraform", "Minio"]
keywords = ["terraform", "minio"]
description = "Using Minio as a backend for Terraform"
showFullContent = false
draft = false
+++

# Hi There! 👋
I've been doing a lot more with self hosting recently and wondered if it were possible
to use [Minio](https://docs.min.io/minio/baremetal/console/minio-console.html#configuration)
as a backend for [Terraform](https://www.terraform.io).

> Spoiler... it is.
> TLDR; link to the code is at the bottom of the article

# Configuring Minio

I already have Minio running locally, so all I needed to do was configure a service
account and create a bucket.

