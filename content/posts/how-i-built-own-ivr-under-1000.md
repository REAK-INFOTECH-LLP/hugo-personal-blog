---
title: "How REAK operates it's IVR/PBX system for $7/mo"
description: "Story of how REAK INFOTECH LLP operates its cloud PBX service with IVR for a very cheap price per month in India"
date: 2020-10-25T01:12:39+05:30
headerImage: true
imageAspectRatio: "is-16by9"
images: ["images/how-i-built-own-ivr-under-1000/cover.jpg"]
imageDesc: "REAK Cloud PBX IVR"
draft: false
year: "2020"
month: "2020/10"
categories: ["Hack","Startup"]
tags: ["tech","setup","ivr","pbx","cheap","wings","asterisk","startup","telephony"]
---

This is the story of how my company runs it's own cloud PBX / IVR system for a really low running and setup cost, and how your startup or small business can benefit from it.

<!--more-->

As a business owner in the modern world, I too was looking for some solutions that can make my life easier. The problem being, I didn't have a receptionist or front-desk at my startup. Me and my early employees never found it useful as we operate like any startup do, lean and self-reliant. We had a small pantry where staff and visitors can get what they need, Simple layout with general public areas immediately next to entrance and very low footfall of customers in the office.

So as you can see we were doing alright except in one area, which was answering phones. We ditched the traditional landline and I had my second number listed out as public contact but once the volume increased it was tedious to deal with. The main problem was routing as when customers called, it had to be routed to one of the devs or me. If it was a business related call I was the recepient, which meant the mobile was getting passed like the last cigarette in a boys hostel at 2AM. *(don't smoke it's not cool - - anymore)*


Also, When our offices were closed there was no call handling unless I took the phone home along with me also we never remembered to say the name of the company like receptionists do after picking up the call making us not-so-professional.

Solution was there but not simple, all we had to do was get a virtual receptionist. But India being India there is some stupid law about VoIP which meant services like Plivo (I highly recommend those guys) and Twilio didn't had Indian numbers and when they did it was crazy expensive, I believe it was about $250/mo with setup fees whereas they charged me $1 a month for a toll-free number in the US.

**So using Plivo or Twilio was out for us mainly because we didn't have as high of a volume to justify the cost**

So I can always look at local solutions right ?, ofcourse we shouldn't be the only one with an issue like this !
Turns out we weren't, There's a ton of services which offer these solutions with Indian phone numbers.
Knowlarity, Exotel just to name a few, but there was a catch; these were more targetted towards sales or call-center setups rather than for business presence but they looked okay for our use-case and pricing was something that was value for money.

But then all of a sudden while going through my news feed I saw something being announced by BSNL, which peaked my interest. It was their WINGS service which was a VOIP only solution with a mobile number. It behaved like any SIM based mobile service but with one important distinction it didn't need a SIM slot of a phone to work, It was a complete virtual solution.

You can basically connect to BSNL Wings SIP Server using any VOIP client and if you were still connected when someone dialled the number you can pickup and talk, or dial an outbound number. It even had International Roaming and ISD, so I can make outbound international calls if needed from the same device. It was a perfect solution for me except one thing, BSNL being BSNL didn't provide any additional servies like IVR or Extensions as an added solution for businesses *(I feel like it's a missed oppurtunity, they would be very popular with small professional businesses)*

With my curious nature and experience with Plivo, I already knew about Asterisk, FreePBX, FreeSwitch etc. Had a devious plan in my head, what if I could just link Asterisk with BSNL Wings ?, this would be the perfect solution to all of my problems.
A simple IVR or Announcement System with Extension Support to tell the caller they did dial REAK Infotech LLP and ability to connect to anyone within my organization including ability for my employees to dial out to clients using the official number strengthening the security aspect *(Yes, call spoofing exists but it's a bit better than my clients trusting any calls with the +91 code)*

### So my month long journey into the unknown begins
\
Since BSNL Wings service was never built for Asterisk, there was pretty much no documentation and BSNL being BSNL there was no one I could talk to clarify my doubts or even get a straight answer if they could offer what I was trying to do as a service.
\
\
So I registered on their website, It was cheap even with an added cost of a small cloud server needed to run my cloud PBX.
Anyways, I chose my preferred number and got the access details *(Had to call a few contacts at BSNL to get it done in 3-4 days including visiting them twice)*,
loaded up [Telephone](https://www.64characters.com/telephone) on my Mac and added credentials for the number, it connected without much fuss. Tried calling a few friends and my brother to check the call quality it was amazing. Kudos BSNL for doing this right, there wasn't any noticeable lag with the service.

This got me excited and I thought to myself how hard could it be ! *(LOL)*
\
\
I started reading up and watching tutorials for Asterisk + FreePBX (FreePBX because I thought GUI would be nice). I searched for the quickest way to get it up and running so I could test, FreePBX website offers ISO which for my test bench using VirtualBox could work just fine but on a cloud service was pretty much useless.

I found a few dockerfiles available on github, and I decided to go with it. This meant I can use any LTS release as base and can also use the server for small scripts and such if the resource utilization was low maximising the value for money metric.
Sounds like a win-win deal so I charged ahead and used tiredofit's Dockerfile to get my system up and running which was quick.

Now for the hard part of configuring the system, I followed Louis Rossmann's PBX tutorial *(I love his board repair videos)* he had pretty much the same requirements I had and the tutorials were great to give a noob understanding of the system.
I configured my system similar to his just to test out the system, It worked ! I was able to see the service being registered at BSNL's SIP Server which means I should be getting incoming calls.
\
\
So time for the real world test, Tried calling the number and it worked !, the number rang and asterisk played out a demo sound. Amazing !
I quickly setup an extension, connected Telephone client to it which worked without any fuss and redirected all incoming calls to my extension. Called the number again and could see my extension ringing but no voice. *Fuck !*

After a good long search rabbit hole, I pretty much narrowed down my issue to NAT settings as I was behind a router in my office, I decided not to waste my energy on it as I will be using a cloud based solution to have users connect from anywhere to the phone system, this way people working from home or on leave can always connect to the system if it was an urgent call.
\
\
So I spin a new $5 Linode on my account to get the tests started, which led me to
\
\
**Problem 1**

BSNL doesn't work with all IP's, It has some sort of filter to identify home IPs vs Commercial (Servers or VPN). I believe this gets removed after IR but didn't work for our connection. So I went on a wild goose chase doing all sorts of nifty stuff to identify what particular rule was deployed for this and how I can bypass it.

Once I devised a way to bypass it, I was able to send registration string from my server and connected to BSNL's SIP server. Good stuff !
So I called the number again and my extension rang, and for the first time could hear sound from my phone.

But couldn't hear anything on my phone, There was no outbound sound from my extension.

*Well fuck me again !*, This leads to
\
\
**Problem 2**

It couldn't be NAT this time because the server now has public IP, but I had a fleeting idea that it might be still some NAT issue as the extension had private IP when looking at SIP logs on server but it shouldn't be an issue (from my understanding of networking).
So I started checking everything from scratch, if it was a voice issue it could be some RTP configuration, codec configuration, Issue with SIP relays as extension was talking to SIP server and then server relaying the same to Wings SIP Server.

Upon deeper inspection turns out RTP was working as intended as well as the codecs I configured, the problem seems to be some of the properties it picked up from the extension's invite which it was relaying to BSNL which their system didn't like.
Basically wings expected all of the attributes to look like it was coming from a single user system, but Asterisk outbound packets to Wings SIP had extension information and handling which Wings SIP didn't like because extension in this case was a third wheel on your third date.
\
\
Solution was pretty simple, modify any attributes which contain reference of extension and make it look like that the asterisk server is the originator of the request and improve asterisk's relay properties so perfect opaqueness exists between Extension <-> Server <-> Wings SIP

Doing that voice started working as expected in case of incoming calls. Excited I setup dialplans for outbound calls and dialled my cell number from the extension and it led me to
\
\
**Problem 3**

Outgoing calls didn't work, even when I had everything setup perfectly it wasn't working. Enabling SIP logs showed me that my INVITE request for the outbound call resulted in a ```403 Forbidden``` response.

Along with this I encountered another issue with Asterisk in Docker, the ping (server <-> BSNL SIP) was massive whenever there was network traffic (extension <-> extension) and the registration drops were very frequent. Docker was not at all a stable platform for running Asterisk + FreePBX and I was loosing hope.
\
\
One fine day I decided to ditch Freepbx and just setup Asterisk (CLI) on the box running latest Ubuntu LTS. My guide this time was Pascom's Asterisk setup (on Youtube) and I dived deep into it. I felt setting up conf files were faster and better than spending hours looking for an option in GUI. I quickly adapted and can reference with Asterisk documentation to make fine tune adjustments I needed for BSNL Wings setup.
\
\
Very quickly I had the entire thing running again but this time a lot more stable, I monitored the registration and it was rock solid. Anytime I dialled the number from my phone it always landed up on the server without fail which instilled confidence.

With docker out of the way the only remaining challenge was Outbound calls and boy was this a challenge, numerous nights got consumed comparing packets captured from my macbook running telephone and asterisk packets from server, comparing both trying to identify the differences and patch them.

And one fine day it happened, I was able to pretty much replicate Telephone data on Asterisk and was even able to hide my server's identity making it look like someone who is running telephone on a computer with a static IP.
\
\
My Asterisk box was perfectly synced with Wings SIP Server, all incoming calls got routed in. Anytime someone dialled an outside number from an extension the call was placed using the Wings server along with IVR setup so the caller could directly dial an extension after the greeting or wait for our ring group so somebody from our team answers the call.
\
\
\
**Now you might ask ?, This is all good but how can I replicate this on my end ?, Where is the code ?**

Unfortunately I decided to not share the code because it affects my setup if people started exploiting the service for robocalls and spam, and this amazing service gets reduced to nothing. 


I've shared on how you can compare and fix the issues, so if you're curious enough you can build the configs yourself.


However, If you're a startup, individual or a business owner who would like to set this up but lack the technical expertise. Please feel free to reach out to me [here](https://ashishsaxena.net/contact), if your reason looks geniune to me I wouldn't mind sharing my entire setup along with the configs.