---
title: "Start Your Own Website / Blog in 60 Seconds for Free"
description: "Get your own website / blog setup for free in less than 60 seconds"
date: 2020-10-12T01:12:39+05:30
headerImage: true
imageAspectRatio: "is-16by9"
images: ["images/personal-blog-screenshot.png"]
imageDesc: "Setup your own website / blog for Free"
draft: false
year: "2020"
month: "2020/10"
categories: ["Tutorial","Web"]
tags: ["tech","setup","website","blog","github pages","travis-ci"]
---

This post will walk you through to setup your own personal website for free using static site generators and leveraging the power of CI/CD tools. Following the tutorial you will be able to deploy and manage your website using git.
<!--more-->
Websites can either be static or dynamic, Personal websites will usually be static in nature as they
don't offer user-based website versions. This makes Static Site Generators really good for public
static websites mainly because of hosting costs, one can use a number of free services like Github
Pages, Netlify etc or use Storage services offered by cloud providers for a more professional
hosting.

Static Site Generators (SSG) are nothing but template engines, reducing the overall effort in building
static websites. Similar to dynamic websites built using React, Ruby, Python, PHP this uses a
template system so you don't need to copy-paste for each page. The difference between dynamic
website built on a server-side language and SSGs is that SSGs build your HTML files locally rather
than at runtime like Server-Side frameworks. This can be even improved by leveraging cloud build
services like Travis-CI, CircleCI or Drone (Self Hosted) to build them automatically as soon as
you've pushed an update and deploy to your hosting platform.

One such Static Site Generator is Hugo which is used for this particular websites, other notable
options are Jekyll, NextJS, Pelican etc. I'll just focus on Hugo as you can then pickup this
template and have your blog up and running in under a minute.

#### Steps

- Clone the git repo, https://github.com/REAK-INFOTECH-LLP/hugo-personal-blog (Start, and take a
   look at any issues that you can participate in)
- Make required changes to config.toml to adjust website parameters, inside content you can edit
   the md files to edit content for any of the pages
- Edit the ```.travis.yml``` file to your requirement, Although the config file will work just fine
   as long as the ```master``` branch is the default for your project
- Generate your GitHub Access token from the following URL :
```https://github.com/settings/tokens```
- Set your access token on TravisCI as a secure variable with key ```GITHUB_TOKEN``` for the repo
that you pushed on github under your account
- Make a commit or manually triger a build and it will automatically deploy your website with GitHub
Pages

You can now go into settings and check the pages URL, accessing which will show you the website as
in the screenshot.
Now you can keep adding blog posts by using the existing post structure as skeleton and personalize
the website for your use.
