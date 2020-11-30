---
title: "Moving away from TravisCI to Github Actions"
description: "Migrating this blog from TravisCI to Github Actions"
date: 2020-11-30T11:49:39+05:30
headerImage: true
imageAspectRatio: "is-16by9"
images: ["images/moving-away-from-travis-to-github/cover.png"]
imageDesc: "Migrate from TravisCI to Github Actions"
draft: false
year: "2020"
month: "2020/11"
categories: ["Programming","Web"]
tags: ["programming","travisci","github","actions"]
---
With the recent changes to TravisCI free account, I walk you through my migration
experience and what I feel like are the pros and cons of each platform
<!--more-->
With the recent changes to TravisCI free plans I felt it was best if I could migrate away,
ofcourse I understand the reasoning behind it.

The service has been struggling for some time now, TravisCI was bought by an investment group and
their priority would have been maximizing profits but providing users free cloud resources
ofcourse goes against it.\
The open source queue in October was something that could never work with active projects
the wait time was around 4-6 hours for my timezone which any active open source project cannot
work with. I wasn't too bothered as my website hardly takes a minute of compute time and isn't
something urgent so I didn't quite feel the need to move away.


This changed with the new open-source changes TravisCI has brought, they now have a static cap which I believe is one-time and they ask to contact them for extra credits which will be reviewed on a case by case basis. The entire thing is worded in a very confusing way.
So in short I decided that since this project is more of a hobby I can pickup Github Actions
and see how it scales up against drone, circleci and travis (build systems that I have used
previously or currently use)


You can check my very first post where I quickly walkthrough my `.travis.yml` file, it's a
very simple workflow. I'll just recap it below

```
Clone Repo
Install Node
Install Node Dependencies
Run NPM script to build CSS from SCSS
Install GoLang and Hugo
Build the website
Sync/Push the folder to gh-pages branch on my repo
```

So I start at the top and start riffling through the documentation looking for examples
to quick start.

It looks pretty straightforward, The root definition is workflow which can comprise of multiple jobs and
each jobs would have instructions to make it work. Since what we're doing here is pretty
simple I decided to use a single job for the entire thing.

So to start with, we need to define a workflow and build params which can be done with

```
name: WebsiteDeploy

# Run this workflow every time a new commit pushed to your repository
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
```

WebsiteDeploy is my workflow name, It is user-editable and shows up on your badge so make sure
you name it accordingly.

Since I use `main` branch to manage website and `master` for pushing changes to upstream or
as a platform to pick changes from upstream and apply to my blog, I am triggering build only on
changes done on the main branch or if there are pull requests to it.
\
\
Next, is something that I implemented at the very end once my entire build was working as
expected. This gives me easy access to change any of the versions and re-use the same build if I
am hosting any other website or some of you want to pick it up for your own use.

```
env:
  NODE_VERSION: '14'
  GO_VERSION: '^1.15.5'
  HUGO_VERSION: '0.79.0'
  CUSTOM_DOMAIN: true
  DOMAIN: 'ashishsaxena.net'
```

Github Actions allows to set Workflow environment variables which can be used within the config
so it makes it very easy to upgrade or downgrade version as needed.
\
\
Next, we configure the job and the OS we need to use for the build version, This can be done
through the following
```
jobs:
  # Set the job key. The key is displayed as the job name
  # when a job name is not provided
  website-deploy:
    # Name the Job
    name: Deploy to Github Pages
    # Set the type of machine to run on
    runs-on: ubuntu-latest
```
Now we get onto the spicy bit,
First thing we need to do is clone our repo on the build system so we can get started

```
    steps:
      # Checks out a copy of your repository on the ubuntu-latest machine
      - uses: actions/checkout@v2
```
Github has some standard defined actions which you can find here [https://github.com/actions](https://github.com/actions)
\
\
Once our repo is cloned, we move onto installing node, which again can be done through the
standard github action
```
      # Install NodeJS
      - uses: actions/setup-node@v1
        with:
          node-version: ${{env.NODE_VERSION}}
```
You can skip defining the node-version and let it use the latest automatically but I prefer
maintaining versions manually.

Once node is installed, we can install dependencies and build css from scss with the following
code

```
      # Install Dependencies
      - run: npm install
      # Build CSS from SCSS
      - run: npm run css-build
```
\
\
Now it's time to install GoLang and Hugo,
Fortunately for us, GoLang installation is simple as its available in the standard actions
```
      # Install Go
      - uses: actions/setup-go@v2
        with:
          go-version: ${{env.GO_VERSION}}
```
But our luck runs out with Hugo, I was able to find some user-built actions for it but
honestly I am not sure of doing that. I am pretty old school and hate the javascript approach
of `npm install hello-world`. It's just a matter or downloading and installing deb so we do it
the old school way
```
      # Install Hugo
      - name: Install Hugo
        run: |
          wget -O hugo.deb https://github.com/gohugoio/hugo/releases/download/v${{env.HUGO_VERSION}}/hugo_${{env.HUGO_VERSION}}_Linux-64bit.deb
          sudo dpkg -i hugo.deb
          rm -rf hugo.deb
          hugo -d website
```
**Wasn't that hard, was it ?**

With the last command there, we basically generate our static website in the `website`
folder.
\
\
\
Now we have everything we need, but the task is to push whatever is inside the `website` folder
to `gh-pages` branch in our repo for github pages to pick it up.

This was probably the hardest part, I tried looking for a solution but couldn't find any. There
were custom actions built by the community but they were full of emoji's in the console log so
I decided to stay away from those.

So being the old school guy I am, Decided going old school hacky way was the best way forward.
Here's what I came up with
```
      # Deploy to gh-pages branch
      - name: Deploy to Pages
        run: |
          git add website/
          git config user.name github-actions
          git config user.email github-actions@github.com
          git commit -m "deploy"
          git subtree split --prefix website -b gh-pages
          git push -f origin gh-pages:gh-pages
```
It's nothing ground breaking or unique, On the contrary when I looked up using subtree for
this was able to find similar examples.

What we're doing here is adding `website/` to our project and then splitting the files inside the
folder to another branch and force-pushing it to our repo.
I don't think there's any logic to maintaining versions of the html files, so force push was
the logical way to nuke the old version and upload the new one.

Important thing to rememeber that nuking branch removes CNAME file so if you're using custom
domain you will have to create the file.
```
      # Set Custom Domain Name
      - if: ${{env.CUSTOM_DOMAIN}}
        name: Set Custom Domain
        run: echo ${{env.DOMAIN}} > website/CNAME
```
Github actions has this nifty if condition, So one can toggle the variables on top and skip
adding the CNAME file to the branch if no domain is set.

And that's the end of it, If you are lazy and looking for the copy-paste you can find it here [https://raw.githubusercontent.com/eclipsed-ninja/personal-website/main/.github/workflows/deploy.yaml](https://raw.githubusercontent.com/eclipsed-ninja/personal-website/main/.github/workflows/deploy.yaml)
