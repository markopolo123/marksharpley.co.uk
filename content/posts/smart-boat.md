+++
title = "Inlets on the water"
date = "2020-05-16"
author = "Mark"
featuredImage = "/img/boat.jpg"
tags = ["linux", "Pi4", "inlets", "boat"]
keywords = ["boat", "smart", "iot"]
description = "Inlets on the water, making a smart boat talkative"
draft = false
+++

# Introduction

Before we dive into the technical aspect of this blog post there should probably
be some explanation for how we got here.

Like many of us, I've at times struggled with the balance of remote work, 
lockdown and my personal life. I've done all the usual stuff - bread baking, 
home IOT setup, and a lot of cycling but there was another itch which required
scratching.

Meet Ionic. She's a 17 metre narrow boat, laid down in 1986. I purchased her
last Christmas and have been slowly renovating her and sailing up the Grand
Union Canal in my spare time. It's a complete detachment from my digital
existance, with lots of manual and dirty jobs. You quickly end up doing things
you'd never contemplated before.

# Okay, so you have a boat

> No DevOps engineer worth their salt could own a boat and not try to connect it
> to the internet.
>
> - Me, probably

This boat is currently pretty basic. The fuel level is measured with a stick.
The water tank level is measured with a stick. Want to know where the boat is?
Look at a map.

There's a lot of potential here for an someone with a pile of Raspberry Pis, an
Internet connection and some spare time.

## Hooking up to the Internet

I have a sim card with a data only contract, plugged into [one of
these](https://consumer.huawei.com/en/routers/5g-cpe-pro/specs/) This lives on
the boat and is plugged into the 12 volt power system which itslf is feed from
two 120 amp hour batteries. The batteries are kept topped up using solar panels.
This system also powers the Pi.

The Router also provides DHCP and WiFi for the boat.

## Starting Simple

I decided to track _Ionic's_ location using a Pi4, a USB GPS dongle and a
prometheus exporter.

A [GPSD](https://gpsd.gitlab.io/gpsd/) exporter for prometheus did not exist so
I [wrote one](https://github.com/markopolo123/gpsd_prometheus_exporter)

Systemd is used to manage GPSD and the prometheus exporter. This is all
installed and configured using an Ansible playbook.

Here's a sample of the prometheus metrics presented from the boat:

```bash
#
# HELP longitude longitude measured
# TYPE longitude gauge
longitude -1.327495167
# HELP latitude latitude measured
# TYPE latitude gauge
latitude 52.054281667
# HELP speed Current speed in knots
# TYPE speed gauge
speed 1.2926536000000002
# HELP altitude Current Altitude in metres
# TYPE altitude gauge
altitude 113.1
```

I use [Prometheus](https://prometheus.io) to monitor the metrics and
[Grafana](https://grafana.com) to visualise them.

# How Inlets helps

Now we have a way to track _ionic's_ location, speed and altitude. I started off
running the whole stack locally, with both prometheus and grafana running on the
boat's Pi4. This isn't particularly useful if I'm not on the boat and frankly, 
it seems a bit wasteful as I have a prometheus and Grafana stack elsewhere
anyway.

So, we need a tunnel - the Pi 4 should automatically use this to connect to my
network so metrics may be scraped from it using Prometheus. Inlets is a great
choice for creating this tunnel I've used it a lot in other projects and like
the simplicitiy of it. Other ways to do this include a VPN like wireguard, SSH
Tunneling with a static IP or dynamic DNS or similar.

![visual explanation](/images/2021-05-16-boat/boat-inlets.jpg)

# Next Steps

So we've seen how inlets tunnels can be used to easily give access to metrics
running on a boat. It's a bit of an _edge_ case but, hey, if the tool fits ; )

There's a bunch more things to do here:

## Adding more metrics.

The sky's the limit here:

* Solar power and battery statistics
* Fuel and water level gauges
* Air quality and temperature

## Flask website to display the boat's location:

![boat](/images/2021-05-16-boat/flask-app.jpg)

## Using push instead of pull

It's not super important to me at the moment, but it would be nice for the boat
to aggregate metrics and push them when it has a connection. Under the current
model, any connection outage would result in lost data. This could also be
useful if low power/burst modes are required. This could be done with the
current stack, or maybe metrics will be shipped using another tool.

## Sending text messages

Via the [Huawei
API](https://github.com/Salamek/huawei-lte-api/tree/master/huawei_lte_api). I'd
like to invoke this using [faasd](https://github.com/openfaas/faasd) running on
the boat's Pi.

Of course the boat still requires a lot of physical labour too, so take the
above as more of a wish list of things I'll eventually get around to.
