---
title: "Start your own blog for free in 60 seconds"
description: "This post will walk you through to setup your own personal website for free using static site generators and leveraging the power of CI/CD tools. Following the tutorial you will be able to deploy and manage your website using git."
date: 2020-10-12T01:12:39+05:30
headerImage: true
imageAspectRatio: "is-16by9"
images: ["images/setup-your-own-website/cover.png"]
imageDesc: "Setup your own website / blog for Free"
draft: false
year: "2020"
month: "2020/10"
categories: ["Tutorial","Web"]
tags: ["tech","setup","website","blog","github pages","travis-ci"]
---

Starting your own personal blog is a good way of sharing your thoughts and tutorials, This tutorial will guide you in setting up a blog using Github Pages
<!--more-->

### Why reinvent the wheel ?
I believe this would be the first question of any WWW-poweruser, Solutions like Medium and Wordpress.org are pretty good to immediately get started with your own blog but the advantage of having your own blog are
* __No stupid licensing__ Take example of Medium if you ever gone through their ToS they get exclusive license to your content that you post on their website. I am not saying they have ill-intentions but they are a free service and they need to make money so if push comes to shove you become their product (or atleast your content).
* __Control__ With your own infrastructure and code, you maintain control of your data as well as availability of your blog. Internet is filled with stories of these companies shutting down blogs or blocking profiles without any notification or redressal mechanism. [Here's an example of the same](https://www.vox.com/2016/7/30/12303070/dennis-cooper-blog-deleted-google)
* __Freedom of Speech and Censorship__  Probably the most important of all, I'm a firm believer in expression of speech but in today's world its harder than ever. Recent developments has made the world a lot more intolerable irrespective of your leaning. Everybody is persuing their agenda and narratives which doesn't suit the agenda get muffled. This enables you to freely express your thoughts  without worrying about the political or social leaning / agenda of your publisher or which country they are based in or majority of investment / control is from.
* __No ads__ Well this wouldn't apply to people who want to commercialize their blog, but for people like me who just want a platform to share their thoughts without any commercial interests I like the idea that there can never be any ads unless I implement them. (If Github starts injecting ads, you can always migrate to others or just buy a shared hosting for couple of dollars)
* __Opensource__ Last but not the least, You can opensource the platform that you built or improve on the existing ones ! This website is entirely open source and if you want a similar website you can just clone it and start using it, isn't it awesome ?

### Enough chit-chat, Show me the code !
Okay, here we go. Static Site Generators are nothing new or complex, It can be defined as *"a dyanmic way to generate static html pages"*. Basically you harness the power of programming to generate static HTML from templates.

There are probably hundreds of Static Site Generators or SSG for short in the wild Jekyll, Hugo, Gatsby, NextJS just to name a few, Here in this tutorial I used [Hugo](https://gohugo.io/) as my choice because I like Go and it felt like a good choice for a simple blog.

Setting up Hugo is pretty simple, You can follow the [instructions](https://gohugo.io/getting-started/installing) or ```brew install hugo``` if you have brew setup on your OS.

Once done, just follow the [quick-start guide](https://gohugo.io/getting-started/quick-start/) to setup your website and install a theme. Hugo offers a brilliant [library of themes](https://themes.gohugo.io/) to choose from.

If you dig the design of this particular website and want to setup something similar to this, Well you're in luck because this website is opensource. You can clone the repo [https://github.com/REAK-INFOTECH-LLP/hugo-personal-blog](https://github.com/REAK-INFOTECH-LLP/hugo-personal-blog)

The above link is to the boilerplate setup yourself version, if you want to get ideas on how I customized it for my own use clone this repo [https://github.com/eclipsed-ninja/personal-website](https://github.com/eclipsed-ninja/personal-website)

### Deploying and Hosting on Github Pages

So I expect by now you should have a website working locally with Hugo. Next step will be to deploy the website to the world. There are two ways of doing this, either build the static website locally and upload the generated files to github and set it up manually. **There's no fun in that !**

**So we do what engineers do, Over engineer it !**
Okay so the idea is the following, you keep your source in a github repo either public or private and build it using online build tools. Benefit being you can now use any device to publish updates to your website and whenever there's a change the online build service will build it and deploy the updated pages !

For my website I chose [Travis-CI](https://travis-ci.org/) to build my website whenever there is an update to source code and push to a different branch on Github to which Github Pages is linked with.

**So how do this work ?**

You can register on the website and activate your repo on Travis dashboard.
![Travis Github Integration](images/setup-your-own-website/link-travis-github.png)

Once done, as long as your repo contains a ```.travis.yml``` file in it's root it should work based on the configuration you provided in the file.

For this blog, I am using the following config
```
dist: bionic
sudo: true
install:
  - curl -LO https://github.com/gohugoio/hugo/releases/download/v0.75.1/hugo_0.75.1_Linux-64bit.deb
  - sudo dpkg -i hugo_0.75.1_Linux-64bit.deb
  - rm -rf hugo_0.75.1_Linux-64bit.deb

script:
  - npm install
  - npm run css-build
  - hugo -d ../website

deploy:
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN  # Set in the settings page of your repository, as a secure variable
  keep_history: false
  local_dir: ../website
  on:
    branch: main
  fqdn: ashishsaxena.net
```

I'll break the down so you can generate your own config easily

```
dist: bionic
sudo: true
```
Pretty self-explanatory that we are instructing travis to use Ubuntu Bionic (18.04) Distribution for this build, I use the same on my local env so its easier to ensure whatever commands I am using will work on remote build too.

I believe sudo is now deprecated, haven't tested it without it but earlier it was required to have this added if sudo was needed mainly for package installations etc.

```
install:
  - curl -LO https://github.com/gohugoio/hugo/releases/download/v0.75.1/hugo_0.75.1_Linux-64bit.deb
  - sudo dpkg -i hugo_0.75.1_Linux-64bit.deb
  - rm -rf hugo_0.75.1_Linux-64bit.deb
```
Installing dependencies, Hugo isn't standard on Travis build systems so we have to download the release and install it. Last line is just cleaning up the deb file post install *(force of habit :D)*

```
script:
  - npm install
  - npm run css-build
  - hugo -d ../website
```
This is the execution block, 
* ```npm installs```, installs the packages ```node-sass``` and ```bulma```
* ```npm run css-build```, builds CSS from SCSS for this website as I am using [bulma framework](https://bulma.io/), it's placed in the assets folder which hugo uses.
* ```hugo -d ../website``` builds the static website inside the ```website``` folder as specified

```
deploy:
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN  # Set in the settings page of your repository, as a secure variable
  keep_history: false
  local_dir: ../website
  on:
    branch: main
  fqdn: ashishsaxena.net
```
This is the deploy block, which contains the configuration needed for proper deployment to github pages.
* ```provider: pages``` Travis recognizes Github Pages and have an internal action mapped to it, so you don't have to write bash commands to push to your repo. By default it pushes to ```gh-pages``` branch when provider is set to pages.
* ```skip_cleanup: true``` This flag is to avoid upload any generated binaries or dependencies usually when testing, since we don't really need that we can skip it
* ```github_token: $GITHUB_TOKEN``` This is how Travis gets access to your account to push the generated static website to your repo in a new branch, You can generate this token from [here](https://github.com/settings/tokens) and set it in the settings section of Travis as shown in the image
![Travis Key Setup](images/setup-your-own-website/travis-settings.png)
* ```keep_history: false``` This is down to personal preference, This flag is set to true cleans out branch history (force git push) on the deploy branch. I don't really want to hold useless data on github so I keep it off. You can turn to true if you want a snapshot of your website at each update.
* ```local_dir: ../website``` Since we only need to deploy the generated static website, that's why we are forcing travis to sync only this folder instead of the source. We instructed hugo earlier to save the output in this particular folder.
* ```fqdn: ashishsaxena.net``` A tricky variable which left me scratching my head for a while, A bit of a backstory the first versions of this config didn't had this flag and I was using custom domain with github-pages but with each update to the blog it removed my fqdn settings. This ensures that custom domain still sticks after update, the value should reflect whatever custom domain you have in your github pages.

### Managing your Blog

Now you have your blog up and running, The next questions is how to manage it ?, adding new posts , updating settings etc.
The setup that we just did basically makes this part easier, it's still no WP-Admin but it's certainly very easy to work with. You can use any device which has git to make updates to your source as soon as you hit ```git push``` the elves will start working and deliver updated content to your website. It's that easy !

You can even use github web interface to make changes to your file and because of Travis listener it will build the release whenever there's a new commit pushed to the repo.

**Share your word with the world, and explore viewpoints of others.**