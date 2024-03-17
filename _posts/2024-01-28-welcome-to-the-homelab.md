---
title: "Welcome To The Lab"
date: 2024-01-28 08:00:00 -0500
categories: [Homelab, Cloudlab]
tags: [homelab, cloudlab, hardware, network, hypervisor, container] # TAG names should always be lowercase
---

## Introduction

Welcome to my new Wiki page for the homelab. I will be using this for now on to keep track of my progress along with helping others with their own homelab journeys. Throughout this article I will be going over the various hardware and software I use to build my homelab. Along with this I will go over some cloud resources that I use and have used in the past that may be helpful for those gettting started for free.

## Hardware

### Hardware Overview

My Lab currently consists of two both a cloudlab and homelab. For a lot of applications I will utize both labs in conjunction for various reasons. A majority of the time I consider my homelab my production lab and the cloudlab my development lab. The last time I wrote it all up I had the following pool of hardware:

- vCPUs: 44
- RAM: 93GB
- Storage: 12TB SSD, 2TB HDD

### Homelab

Normally I would say this is the most important part but I believe in cheap hardware and God bless China for that one. As long as it is well reviewed online I am willing to give any hardware a try. My number one tip is to buy or prepare to upgrade units with more memory. Almost all of my units have less than ten percent CPU utilization but always are running out of memory. Right now I have the luxury of many nodes so I can split my services across my lab but honestly you could easily run off one physical node with a lot of memory.

![Desktop View](/assets/img/welcome-to-the-homelab-reference.jpg){: width="400" height="700" }
_A current image of my homelab running out of my apartment. Cardboard is king._

#### My current homelab compute hardware is:

- Protecli VP2410
  - 4 vCPU (amd64), 8GB RAM, 500GB SSD
- Beelink U590
  - 4 vCPU (amd64), 8GB RAM, 500GB SSD
- Beelink EQ12 Pro
  - 8 vCPU (amd64), 16GB RAM, 500GB SSD
- AOOSTAR R5
  - 12 vCPU (amd64), 64GB RAM, 500GB SSD, 2x 4TB SSD
- Raspberry Pi 2B
  - 4 vCPU (armhf), 1GB RAM, 128GB SD
- My Old Desktop
  - 12 vCPU (amd64), 16GB RAM, 250GB SSD, 2x 1TB HDD, RTX 2060 6GB

#### My current homelab network hardware is:

- Protecli VP2410
  - 3x 1G RJ45
- MokerLink 2G08110GS
  - 8x 2.5G RJ45, 1x 10G SFP+
- TL-SG108E
  - 8x 1G RJ45
- ASUS RT-AC3200
  - 2x 5GHz, 1x 2.4GHz, 4x 1G RJ45

### Cloudlab

#### My current cloudlab hardware is:

- Cloud #1
  - 1 vCPU (amd64), 1GB RAM, 50GB SSD
- Cloud #2
  - 1 vCPU (amd64), 1GB RAM, 50GB SSD

### Cloud Resources

I have tested many cloud services. Most of these run promotions and/or free credit to be used over a month or two. A lot offer free services for students. I highly suggest you abuse the free trials for learning but for production I highly recommend you do the math and consider running a homelab over a cloudlab. Like my homelab suggestion the biggest concern with cloud resources is the cost for adedite RAM. All that being said from my experience the following providers have been solid:

- [Linode](https://www.linode.com/)
- [Digital Ocean](https://www.digitalocean.com/)
- [Vultr](https://www.vultr.com/)
- [Google Cloud](https://cloud.google.com/free)
- [Oracle Cloud](https://www.oracle.com/cloud/free)

Below are two popular lists that the community does their best to keep up to date about all the free cloud resources (some with trial periods):

- https://github.com/ripienaar/free-for-dev
- https://github.com/cloudcommunity/Cloud-Free-Tier-Comparison

## Software

### Hypervisors

I have tested quite a few hypervisors over the hears. It started with [XenServer](https://www.xenserver.com/). Then as I grew up all the rage at the time was [VMware](https://www.vmware.com/products/esxi-and-esx.html). I went off to college, had a job and got my first piece of homelab hardware. I went to put VMWare but it had issues and crazy enough this new product called [Proxmox](https://www.proxmox.com/en/proxmox-virtual-environment/overview) installed just fine and I haven't looked back since.

### Containers

For almost all of my containers I use [Docker](https://www.docker.com/). It is by far the easist and most popular way to run containers from my experience. When you have trouble, troubleshooting docker, docker-compose and its associated tools is a breeze. K8s is a wildly different story. I have used [K3s](https://k3s.io/) in the past along with [K8s](https://kubernetes.io/) but every time I end up frustrated I just restart the trusty docker container. My favorite management tool for containers is [Portainer](https://portainer.io/). If you are using K8s or K3s you can also try out [Rancher](https://rancher.com/). I utilize a private GitHub (replicated to my self hosted GitLab) repository for all configurations but will gladly consider opening them up if it would help others. Below are a few of my favorite public repositories where I get most of my boilerplates and configurations:

- https://github.com/techno-tim/launchpad
- https://github.com/ChristianLempa/boilerplates
- https://github.com/JamesTurland/JimsGarage
- https://github.com/awesome-selfhosted/awesome-selfhosted

### What is Next

Over the next few articles I plan deep diving into my homelab and cloudlab. We will start off with a detailed hardware tour and how I set it all up. Then we will go over how I choose to setup software on all of the hardware. If you think you will enjoy the upcoming content join the [Discord](https://discord.gg/DJDT3cKMGx), follow my [Twitter](https://twitter.com/SquirrelWeeed), [Twitch](https://www.twitch.tv/squirrelonweed), [YouTube](https://www.youtube.com/@SquirrelOnWeed) and [GitHub](https://github.com/squirrelonweed). Thanks for reading and happy homelabbing!
