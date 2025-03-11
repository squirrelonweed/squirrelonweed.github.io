---
title: Cloud Services
date: 2022-06-20 17:00:00 -400
categories: [homelab, cloud]
tags: [cloud] # TAG names should always be lowercase
---

# Introduction

I had quite a few people asking me about hosting a server of their own, specifically one they don't have to have running against their electric bill. I usually try to help people find low power, efficient computers such as Raspberry Pi's, Single Board Computers, developing custom computers or repurpose old hardwre for another life in their homelab. In this article I want to go over some the mystical cloud and show you how easy it really is.

# Providers

In my [Hello Homelab](https://wiki.willswildworld.com/posts/hello-homelab/) I briefly went over three of my most used cloud solutions but there are truly many out there to leverage. Below I will go over a good list cloud providers that you can sign up for (for free). Most of these providers offer everything from free trials to high end systems better than most home computers. The higher end system you go the worse the ROI is but we will talk about that towards the end of the article.

## Major Providers

- [DigitalOcean](https://digitalocean.com/)

  [DigitalOcean's](https://try.digitalocean.com/freetrialoffer/) $100, 60 day free trial is hard to beat as it gives you the most monopoly money to spend. They have an official [YouTube](https://www.youtube.com/linode) Channel to help you get things started. The Digital Life on [YouTube](https://www.youtube.com/c/TheDigitalLifeTech) also tends to use strictly Digital Ocean for their tutorials as supplimental content.

- [Linode](https://www.linode.com/)

  [Linode's](https://www.linode.com/pricing/) $50, 60 day free trial is a pretty solid offering. They are open source friendly and also have a complimentary [YouTube](https://www.youtube.com/linode) to help you get familar with their ecosystem.

- [Google Cloud](https://cloud.google.com/)

  [Google Cloud](https://cloud.google.com/free) has a completely free tier as well as a $300, 90 day trial that you can take advantage of. Check out their documentation [here](https://cloud.google.com/free/docs/gcp-free-tier).

- [Amazon Web Services](https://aws.amazon.com/)

  [AWS](https://aws.amazon.com/) also has a [free trial](https://aws.amazon.com/free/) that you can leverage. Their policy is a bit confusing at times so make sure you read through all their restrictions so you don't end up with a nice credit card bill.

- [Microsoft Azure](https://azure.microsoft.com/)

  [Azure](https://azure.microsoft.com/) like Google Cloud and Amazon also has a [free tier](https://azure.microsoft.com/en-us/pricing/free-services/) that looks very similar to Amazon's policy. Assumingly due to their ties with many American univerisities if you have a University email address you may receive a [free $100 credit](https://azure.microsoft.com/en-us/free/students/) to use.

## Other Providers

Belo are a few other honorable mentions for your cloud needs. Most of these services also offer a free trial or free tier of some capacity.

- [ServerSpace](https://serverspace.us/)
- [Vultr](https://www.vultr.com/)
- [Kamatera](https://www.kamatera.com/)

# Cloud vs Self Hosting

That is a loaded topica and many more reputable sources can better help you decipher that question but here are my opinions and comparions amongst the two.

## The Cost

Cloud hosting can be pretty expensive depending on the plan that you choose aka the hardware plus software solutions provided by the host. The main multipliers are typically core count, RAM, storage and network bandwidth. Most sites offer a pricing comparison these days to help you find the best service for your wallet.

To make the best decisionDigitalOcean's old [calculator](https://web-platforms.sfo2.cdn.digitaloceanspaces.com/pricing-calculator/index.html) is a great start but it is recommended to get the current prices from each provider directly. Below are a few of those calculators.

- [DigitalOcean](https://www.digitalocean.com/pricing/calculator)
- [Linode](https://www.linode.com/cloud-pricing-calculator/)
- [Google Cloud](https://cloud.google.com/products/calculator)
- [Amazon Web Services](https://calculator.aws/)
- [Azure](https://azure.microsoft.com/en-us/pricing/calculator/)

## The Risk

Hosting in the cloud can be scary depending on how you know about the services you are running on your appliance. When you publicly host (whether at home or in the cloud) you are putting a target on your back for hackers as most homelabs do not put the same security measures in place as big businesses.

For just getting started with learning how to interact and safely control infrastructure I highly recommend installing Linux in a virtual machine on your current computer. If you only have a phone or tablet then get a provider like DigitalOcean who allows you have a network firewall to protect you (remember to set it up). Once you understand how to better protect yourself and your assets online you are free to begin opening ports on that network firewall so you can truly expose your server to the world.

# Final Thoughts

If you have the ability to self host, do it! In the United States most individuals have broadband internet (25Mbps download and 3Mbps upload). This is good enough for basic webpages and simple game servers. In terms of hardware I have written a guide about my hardware in my [Hello Homelab](https://wiki.willswildworld.com/posts/hello-homelab/) article. You can also find plenty of good offerings on Facebook Marketplace, Craigslist, Ebay, and Amazon for decent prices.

For reference, the cloud provider that I'm currently using costs $40 a month or $500 a year just for the hardware. Like renting an apartment you are giving away the money instead of investing it in local hardware. For that price you can find (even during the shortage) good mini computers that would have similar performance such as the [Intel NUC 11 NUC11PAHi5](https://www.amazon.com/dp/B09BKRXXNZ/). Another good source of validated hardware has been explored by a YouTube channel called [ServeTheHome](https://www.youtube.com/c/ServeTheHomeVideo). They did a series called [TinyMiniMicro](https://www.youtube.com/playlist?list=PLC53fzn9608B-MT5KvuuHct5MiUDO8IF4) where they go over small, power efficient hardware that would work wonderful as a home server.
