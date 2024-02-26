---
title: "Diving into the deep"
date: 2024-02-24
tags: [networking, xfinity, evil, cg-nat, homelab, rant]
---
# Diving into the deep
Recently I had a little *snafu* with Xfinity. I have been an Xfinity customer for more than a decade. In my area, it has really been the *only* option for high-speed internet connections. I have always felt uneasy about the leverage that Xfinity has had over the market. I hate the equipment fees, and the lack of good customer service. I refused to use an Xfinity router or modem, instead opting to buy my own.

A few years ago they implemented a data cap, and I — untrusting of the monopoly-induced power of the company — purchased a UDM Pro SE and started measuring my own data usage. Throughout these years the usage reported by Xfinity was typically higher than my own measurements, but within a reasonable margin of error. However, that changed in December of 2023. During that month the UDM Pro measured about 800GB of throughput, and Xfinity reported a whopping **2400GB** of usage! **Three times more**!

I walked in to the nearest Xfinity store intending to ask for a service ticket. ~~However, I was told in no uncertain terms that ***the Lord God of Xfinity*** in it's ***mighty Glory*** and ***omnicient knowing*** is the *only* source of **truth**. My own paltry attempt at measuring throughput was meaningless. *Xfinity's* mighty judgement would not be questioned! How dare I question it?~~ *Okay, okay... those were not their exact words, but they are not **that** hyperbolic either.* They refused to submit a service ticket on my behalf, and told me to quietly pay the bill of over $300, ~~and that I should leave with a *smile* on my face~~.

**Instead I left with a solid resolve to cancel my account.** I saw the commercials for T-Mobile Home Internet, advertised for $50 a month with *unlimited* data usage. It actually ended up being slightly cheaper than that since I am an existing T-mobile customer. It offers better speeds than any other ISP in my area besides Xfinity, and I am happy with my choice (so far). A week or so later I closed my Xfinity account and I am not looking back.

T-mobile home internet brings some additional challenges. Namely, I am now sitting behind a messy CG-NAT. I do not have a public IP address to access my home network. If you google around a bit, most people solve this by using a *Cloudflare* tunnel. I have used them before, and it is an easy and effective solution, even providing ssl certificates automatically. But, *I* prefer doing things the *hard* way. It's the only way to learn something new.

This post is getting quite long. Now that you know the backstory, I will post the *exciting* details of my own personal solution to these problems.

Time to make more coffee.
