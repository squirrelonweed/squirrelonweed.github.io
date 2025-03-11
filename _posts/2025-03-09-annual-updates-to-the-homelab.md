---
title: "2025 Updates To The Lab"
date: 2025-03-09 08:00:00 -0500
categories: [Homelab, Cloudlab]
tags: [homelab, cloudlab, hardware, network] # TAG names should always be lowercase
---

## Introduction

Welcome to third update to the homelab! I have been hard at work spending all my spare bonuses on more hardware. I am going to break down the hardware I have and the big changes I have made over the last year. The biggest two things I got was Network and Power Backup Hardware. Hope this helps you pick out your first homelab hardware without getting overwhelmed or wasting too much money. At the end of this article I am including my first timer mix of cloud and homelab hardware for the best price with baseline software suggestions.

## Hardware

### Hardware Overview

I haven't grown too much since my last update in 2023. I did pick up a CyberPower 1500vA UPS for my homelab. Along with that I made some major changes to my networking along with getting some more hardware for local AI (I was just doing LLMs, now piviting into Image / Video Generation). I now have eight nodes in my homelab and four in my cloudlab. Over the last few years I have focus on developing on my homelab and deploying finished products to the cloudlab. This has allowed me to keep my services up to date while also retaining the ability to learn from the bleeding edge updates. My cluster now consist of the following hardware:

- Nodes: 12
- vCPUs: 56
- RAM: 164GB
- Storage: 4TB NVMe, 12TB SSD, 2TB HDD
- vGPUs: 2

### Homelab

The majority of my homelab changes have revolved around the networking side of my homelab. I first replaced a redundant pair of pfSense VMs sharing my single incoming ISP connection. This freed up two hosts to focus on my Docker Swarm and K3s cluster (I refused to use them for anything but networking prior due to the critical nature they served). Along with this I have replaced all my Mokerlink switches with a high capacity one as well. For routing I picked up the [Unifi Dream Machine 7](https://store.ui.com/us/en/category/all-cloud-gateways/products/udr7) and for switching I picked the [Unifi Pro Max 24](https://store.ui.com/us/en/category/all-switching/products/usw-pro-max-24).

![Desktop View](/assets/img/homelab-2025-update-server-rack.jpg){: width="400" height="700" }
_An image of my homelab running in my apartment. I love seeing this every day._

#### My current homelab network hardware is:

- Unifi Dream Machine 7
  - 4x 2.5G RJ45 (1x 15w PoE), 1x 10G SFP+, WiFi 5, 6, 7
- Unifi Pro Max 24
  - 16x 1G RJ45, 8x 2.5G RJ45, 2x 10G SFP+

#### My current homelab compute hardware is:

- CWWK J6412
  - 4 vCPU (amd64), 16GB RAM, 500GB NVMe, 500GB SSD
  - 4x 2.5G RJ45
- Protecli VP2410
  - 4 vCPU (amd64), 16GB RAM, 500GB NVMe, 500GB SSD
  - 4x 1G RJ45
- Beelink U59
  - 4 vCPU (amd64), 16GB RAM, 500GB SSD, 250GB SSD
  - 2x 1G RJ45
- Beelink EQ12 Pro
  - 8 vCPU (amd64), 16GB RAM, 500GB SSD, 250GB SSD
  - 4x 2.5G RJ45
- AOOSTAR R5
  - 12 vCPU (amd64), 64GB RAM, 2x 500GB NVMe, 2x 4TB SSD
  - 2x 2.5G RJ45
- Raspberry Pi 2B
  - 4 vCPU (armhf), 1GB RAM, 128GB MicroSD
- Raspberry Pi 5
  - 4 vCPU (arm64), 8GB RAM, 256GB NVMe
- My Old Desktop
  - 12 vCPU (amd64), 32GB RAM, 2x 500GB NVMe, RTX 2060 6GB, RTX 4060Ti 16GB
  - 1x 1G RJ45, 2x 10G SFP+

### Cloudlab

#### My current cloudlab hardware is:

- Oracle #1
  - 1 vCPU (amd64), 1GB RAM, 50GB SSD
- Oracle #2
  - 1 vCPU (amd64), 1GB RAM, 50GB SSD
- RackNerd #1
  - 2 vCPU (amd64), 3.5GB RAM, 100GB SSD
- Hostinger #2
  - 4 vCPU (amd64), 16GB RAM, 200GB SSD

### Cloud Resources

This time around my new cloud resource additions had two very specific purposes. I wanted to super fast (in terms of bandwidth) VPS to act as a reverse proxy. Along with this I wanted a 24x7 game server that I could use to host multiple games. I also used this opportunity to learn how to host my own service like [Shockbyte](https://shockbyte.com/) using [Pterodactyl](https://pterodactyl.io/). If you are interested I have an older write up that goes over the basic of how to setup a game server [here](https://wiki.squirrelonweed.com/2024-02-01-hosting-your-own-game-server/). With now having purchased servers from Linode, DigitalOcean, RackNerd, and Hostinger I have stong opinions on which ones I prefer. For the best for beginners I recommend Linode or DigitalOcean. For a very similar but slightly cheaper option go with Hostinger. For the advance Linux users RackNerd is the absolute cheapest option but requires knowledge of how to secure your server (RackNerd includes no security by default such as WAF).

### Fist Timer Suggestions

If I had to go back to 2022 when I first started my homelab journey I would certainly would have a different set of hardware. Don't get me wrong, I am still over the moon happy with what I have but can certainly attest this build would save a lot of money while consuming much less power.

If I had to begin rebuild my lab today I would go with the following hardware:

#### Homelab

##### Network

If you want the cheapest options with ninty percent of the benefits te Cloud Gateway Ultra will suit you well. If you want great WiFi and the full Unifi package I recommend the Dream Machine 7.

- [Unifi Cloud Gateway Ultra](https://store.ui.com/us/en/category/all-cloud-gateways/products/ucg-ultra) - $129
  - 5x 1G RJ45

- [Unifi Dream Machine 7](https://store.ui.com/us/en/category/all-cloud-gateways/products/udr7) - $279
  - 4x 2.5G RJ45 (1x 15w PoE), 1x 10G SFP+, WiFi 5, 6, 7

##### Compute

If you want the cheapest option that will work grab one or up to three of the Beelinks so you can practice a plethora of technologies such as CEPH, HA and all the other tech such as Kubernetes / Docker Swarm. If you have the spare money or wish to save up for a single efficient machine the Mac Mini M4 base model will smoke two Beelinks combined plus it has the built in GPU (16GB of unified RAM) so it can run AI (LLM, Image Generation) with ease. Combine two M4s over Thunderbolt to run 32GB models (or purchase the M4 with 32GB RAM for an eye watering $200).

- [Beelink EQ14](https://www.amazon.com/Beelink-Lake-N100-Mini-Computer-Supports-Home-Server/dp/B0C339KVH9) - $199
  - 4 vCPU (amd64), 16GB RAM, 500GB NVMe

- [Mac Mini M4](https://www.amazon.com/Apple-2024-Desktop-Computer-10%E2%80%91core/dp/B0DLBTPDCS) - $500
  - 10 vCPU (arm64), 10 vGPU, 16GB RAM, 256GB NVMe

#### Cloudlab

The Oracle Free Tier is as it states, free. I highly recommend leveraging this gracious offer by Oracle for your learning purposes. They provide plenty of bandwidth at good speeds along with good hardware. They include public and private IPs so that you they can interact in their LAN safely with each other. Good for setting up dependant services such as a few websites on one and hosting a shared database on the other.

- Oracle #1 - Free
  - 1 vCPU (amd64), 1GB RAM, 50GB SSD
- Oracle #2 - Free
  - 1 vCPU (amd64), 1GB RAM, 50GB SSD

#### Services / Software Suggestions

Regardless of what hardware you choose having a well thought out software stack is important. In both terms of making your lab accessible but more importantly secure. All of these services besides your DNS name (which are typically cheap or alternatively free if you are okay with odd website names). Cloudflare will host your DNS, SSL and WAF. You can use these three services by Cloudflare to almost entirely secure your lab. Pangolin a Reverse Proxy that utilizes Wireguard tunnels so that you can access your homelab through your cloudlab without having to punch holes through your router. Plus it is secure!

##### Services

- [Cloudflare](https://www.cloudflare.com/)
    - For your DNS, SSL and Domain Registrar.

##### Software

- [Pangolin](https://github.com/fosrl/pangolin)
    - Reverse Proxy
    - Load Balancer
    - Authentication Server

### What Is Next

During my last annual homelab update (two years ago) I mentioend wanting to get into K3s. I got into it but never fully deployed my production on it. Instead I went with Docker Swarm. Maybe this year I will deploy my production on K3s just for fun. I would also like to be more rutinely updating this forum with new content. I would like to document setting up your first vGPU node in your homelab along with documenting my hybrid lab now that I have a secure way of interconnecting between my home and cloud lab. 

If you think you will enjoy the upcoming content join the [Discord](https://discord.gg/DJDT3cKMGx), follow my [Twitter](https://twitter.com/SquirrelWeeed), [Twitch](https://www.twitch.tv/squirrelonweed), [YouTube](https://www.youtube.com/@SquirrelOnWeed) and [GitHub](https://github.com/squirrelonweed). As always, thanks for reading and happy homelabbing!
