---
title: "OpenDNS on a Dynamic IP was a Mistake"
description: "How our internet was made inaccessible because of OpenDNS and some random guy"
date: 2021-07-12T00:00:00+05:30
headerImage: true
imageAspectRatio: "is-16by9"
images: ["images/opendns-internet-shutdown/cover.jpg"]
imageDesc: "Open DNS Internet Shutdown"
draft: false
year: "2021"
month: "2021/07"
categories: ["Tech", "Thoughts"]
tags: ["tech","security","opendns","content-filtering"]
---

Some of my non-geeky colleagues of mine were getting bogged down by stupid ads and landing on shady websites.

So for the sake of protecting my Gotham, I decided I'll just implement a simple DNS-based system. This way the batmans-and-robins of my company can bypass it if needed and the general users of the network at least have basic protection.

<!--more-->

I knew about 2 such services from my experience, the First being Komodo Dome which turns out has gone full-blown enterprise and it was a mess to understand how to set it up without the useless "Click here for Demo" form.

So I turned to OpenDNS, was a breeze signing up and within minutes I was where I needed to be setting up domain blocks and selecting categories to block, Next came the option to automatically update IP to my account, So adding a quick cron job on our router was the end of it. I tested with the bad guy domain and it seemed to work out fine.

Few days go by and everything seems to be going well, But one fine day internet doesn't seem to work out for the majority of the users but works for the rest of us (power users mostly). Had no fucking clue as to what went wrong we started looking into routers/switches, pinging and tracing local running services and everything seems to be working as expected.

I took my phone out to google something and found out that the internet isn't working on it and then it clicked me, it must be DNS related as I usually use cloudflare on my desktop but keep the default running on my phone.

So I login to my OpenDNS account to check if I messed any of the settings up or maybe the IP update didn't trigger for some reason ?, Everything looks fine but it shows an outdated IP.

Looks like OpenDNS doesn't expire the IPs added to the other accounts and don't think there's an override if they are not being used or the same is updated on another account, so someone signed up to the service using the IP we have now and somehow blocked almost all of the websites, or at least all of the major ones.

Hope this helps others who might be looking to use these services, We finally set up a local DNS server and set up our blocklist as it was much easier and something that should have been done initially. Contacted OpenDNS about this but I am yet to hear back from them, If I receive any updates from their end regarding any changes to avoid this I'll update the blog post but honestly one should stay away from these services and just run the DNS Server locally.
