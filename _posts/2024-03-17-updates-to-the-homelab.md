---
title: "Updates To The Lab"
date: 2024-03-17 08:00:00 -0500
categories: [Homelab, Cloudlab]
tags: [homelab, cloudlab, hardware, network, hypervisor, container] # TAG names should always be lowercase
---

## Introduction

Welcome to second update to the homelab! Recently I have been blessed. Instead of saving it I spent it on computers. I truly believe I made a wise investment! We finally bought a real rack! Am I a YouTuber yet? I have also purchased another domain name, fanless appliance, new managed switch and thanks to the new switch I was had ability to setup highy availble routers.

## Hardware

### Hardware Overview

In case you didn't read the first article my lab currently consists of two both a cloudlab and homelab. I have seven nodes in my homelab and two in my cloudlab. Due to the cheaper electricity it is truly cheaper in the age of AI for me to run locally than in the cloud with Google or DigitalOcean. For a lot of applications I will utize both labs in conjunction. Sometimes I use my database in the cloud with an applicaiton running locally. An idealogy that recently changed is that I now strictly deploy finished products to the cloudlab and develop on the homelab when resources allow. I currently have the following pool of hardware:

- Nodes: 9
- vCPUs: 50
- RAM: 124GB
- Storage: 2TB NVMe, 12TB SSD, 2TB HDD
- vGPUs: 1

### Homelab

Once again I went and bought ye ole AliExpress specials. Locked in a CWWK fanless unit with an Intel J6412 for what I felt like was free. This thing has been a champ since the beginning. IOMMU works like a charm for my router. The switch I bought has the lowest effort management web interface and few options but the throughput is spot on. The biggest change for me (in my opinion) was the rack. It added some needed professionalism and organanization to my office.

![Desktop View](/assets/img/homelab-update-server-rack.png){: width="400" height="700" }
_An image of my homelab running in my apartment. I love seeing this over the cardboard in the corner._

#### My current homelab compute hardware is:

- CWWK J6412
  - 4 vCPU (amd64), 16GB RAM, 500GB NVMe, 500GB SSD
  - 4x 2.5G RJ45
- Protecli VP2410
  - 4 vCPU (amd64), 8GB RAM, 500GB NVMe, 500GB SSD
  - 4x 1G RJ45
- Beelink U59
  - 4 vCPU (amd64), 8GB RAM, 500GB SSD
  - 2x 1G RJ45
- Beelink EQ12 Pro
  - 8 vCPU (amd64), 16GB RAM, 500GB SSD
  - 4x 2.5G RJ45
- AOOSTAR R5
  - 12 vCPU (amd64), 64GB RAM, 500GB NVMe, 2x 4TB SSD
  - 2x 2.5G RJ45
- Raspberry Pi 2B
  - 4 vCPU (armhf), 1GB RAM, 128GB SD
- My Old Desktop
  - 12 vCPU (amd64), 16GB RAM, 500GB NVMe, 2x 1TB HDD, RTX 2060 6GB
  - 1x 1G RJ45, 2x 10G SFP+

#### My current homelab network hardware is:

- CWWK J6412
  - 3x 2.5G RJ45
- Protecli VP2410
  - 3x 1G RJ45
- MokerLink 2G08110GS
  - 8x 2.5G RJ45, 1x 10G SFP+
- MokerLink 2G08110GSM
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

Besides hardware my biggest changes were in the cloud. My new domain name which you are on right now is [squirrelonweed.com](https://squirrelonweed.com). Getting the second domain name was cheap. Having two domains with Cloudflare allows me to apply different rules, enforce different protocols, security postures and more to each one. It also allows me to continue with the pattern of seperating dev and prod. Now I can break my own little slice of internet without dropping all my good DNS. The next big change was to leverage cloud databases. Running locally is fine and dandy but it is also nice to have a secondary / backup database. I have integrated [aiven.io](https://aiven.io/) and [neon.tech](https://neon.tech/) into my stack. Aiven.io provides Postgres, MySQL and Redis while Neon.tech provides Postgres.

### What Is Next

Not too sure. Might go down the rabbit hole on the how I fully leverage my lab. Might skip that under the assumption everyone has their hardware setup at this point. I recently setup a k3s cluster with longhorn and rancher. Also recently build my own LLM. I think either topic would be something most people could run and benefit from learning it. If you think you will enjoy the upcoming content join the [Discord](https://discord.gg/DJDT3cKMGx), follow my [Twitter](https://twitter.com/SquirrelWeeed), [Twitch](https://www.twitch.tv/squirrelonweed), [YouTube](https://www.youtube.com/@SquirrelOnWeed) and [GitHub](https://github.com/squirrelonweed). Thanks for reading and happy homelabbing!
