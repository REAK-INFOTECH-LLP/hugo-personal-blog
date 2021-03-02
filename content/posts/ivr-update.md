---
title: "Update to REAK's PBX / IVR system"
description: "Updated Story of how REAK INFOTECH LLP operates its cloud PBX service with IVR for a
very cheap price per month in India with config files available for download"
date: 2021-02-27T00:00:00+05:30
headerImage: true
imageAspectRatio: "is-16by9"
images: ["images/how-i-built-own-ivr-under-1000/cover.jpg"]
imageDesc: "REAK Cloud PBX IVR Update"
draft: false
year: "2021"
month: "2021/02"
categories: ["Hack","Startup"]
tags: ["tech","setup","ivr","pbx","cheap","wings","asterisk","startup","telephony"]
---

An update to the earlier blog post regarding the PBX / IVR System, Due to massive interest
I've decided to make the asterisk config available publically.

<!--more-->

This is an update to a previous blog post I wrote [How REAK operates it's IVR/PBX system for
$7/mo](https://ashishsaxena.net/posts/how-i-built-own-ivr-under-1000/)

There has been tremendous interest in that particular blog post from what I can see on my emails
and traffic analysis, so I thought I'll just make the entire thing public with a few
modifications.

#### Batteries Excluded
The code doesn't include all of the bypasses that I use, but in it's current form it's more than
enough to get asterisk established with BSNL. Once you frequently start using the system
then you will need to look at your asterisk logs and make the required changes to ensure
calls flow freely.

The mitigations are pretty basic and should be obvious to anyone who pays attention to asterisk
logs. This has been put in place to avoid misusing this service and to deter robocallers from
using a simple & cheap system for spam.


\
\
You can find the asterisk config files here on my github : [Simple Asterisk
Deployment](https://github.com/eclipsed-ninja/simple-asterisk-deployment)

For any queries, I would prefer if you can open up an issue on the repo
