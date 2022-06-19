---
title: Hello HomeLab
date: 2022-06-19 12:00:00 -400
categories: [homelab,hardware]
tags: []    # TAG names should always be lowercase
---

# Introduction

Welcome to Will's Wild Wiki. This is new home for all of my my documentation moving forward. A lot of my development will be out on GitHub but figured I would make easier to read documentation for myself (and hopefully you too). I am also using this site in conjunction with GitHub, LinkedIn and Discord to connect with people who are interested in HomeLabbing. 

In the rest of this article I will go over the hardware and software solutions that I am using in my lab.

# My  HomeLab

After a metric ton of trial and error I have ended up at this config below. Looking forward I would like to get a newer Raspberry Pi but will hold off until prices come down. I may buy a cheap mini PC or Intel NUC to replace it at some point. A few months ago I starting mixing in a cloud resource to add some headaches to my life as well as some good practical experience. 

## Networking

- Protectli Vault Pro VP2410 (8GB RAM, 256GB SSD, Wireless Adapter scavenged from old laptop) 
- TP-Link 8 Port Managed Gigabit Switch (TL-SG108E)
- Asus RT3200 (running as primary AP)

## Computers
- Gaming PC: AMD 5800X, 32GB RAM, Nvidia RTX 3080Ti, 1TB Samsung 970, 2TB Samsung 870, 250GB Samsung 850, 2TB Seagate Firecuda.
- Custom PC: AMD 3600, 16GB RAM, 1TB Samsung 870, 2x 1TB HDD, 
Intel PRO/1000 PT Dual NIC + onboard NIC
- Protectli Vault Pro VP2410: Celeron J4125, 8GB RAM, 256GB SSD
- Raspberry Pi 2B: ARM A7 CPU, 1GB RAM
- Cloud of the Month: 2-4 Cores, 4-8GB RAM, 100GB Storage

## Software
To keep things simple and easy everything that can is running Proxmox. Proxmox is a great vessel for LXC containers. Almost every container I have is LXC and is running docker-swarm. Between Proxmox HP and Swarm's HP I have solid availbility. To manage authentication I use Teleport along side Nginx (currently piviting to Authelia) to keep things locked down especially now that I'm in the cloud.

Below is a list of Software Solutions that I have piloted or are currently using:

Networking:
- [Cloudflare](https://www.cloudflare.com/)
- [Cloudflare Tunnel (Argo)](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [pfSense](https://www.pfsense.org/)
- [Ngnix Proxy Manager](https://nginxproxymanager.com/)
- [Traefik](https://traefik.io/)

Hypervisors:
- [Proxmox](https://www.proxmox.com/)
- [XCP-ng](https://xcp-ng.org/)
- [VMware EXSI](https://www.example.com)

Infrastructure As Code:
- [Ansible](https://www.ansible.com/)
- [Terraform](https://www.terraform.io/)

Containers:
- [Docker](https://www.docker.com/)
- [Portainer](https://www.portainer.io/)
- [K8s](https://kubernetes.io/)
- [Rancher](https://rancher.com/)

Remote Access:
- [Teleport](https://goteleport.com/)
- [Cockpit](https://cockpit-project.org/)

Other Services / Tools:
- [Ventoy - Multiboot(able) Flash Drive](https://www.ventoy.net/en/index.html)
- [Pterodactyl - Game Server](https://pterodactyl.io/)
- [LinuxGSM - Game Server](https://linuxgsm.com/)
- [Wiki.js - Documentation](https://js.wiki/)
- [NordVPN - Good Router/Linux VPN](https://support.nordvpn.com/Connectivity/)
- [title](https://www.example.com)

# How we got here
I was watching YouTube one day and found a channel called "Learn Linux TV" (amazing channel btw) and was sitting there thinking I have two computers (just my RT3200, old PC and Pi at the time) and nothing better to do. Please install Ubuntu on them and see what we can do. From there it evolved into running containers through Docker. Them writing Docker-Compose files. Then learning about Swarm and how I can setup load balancing (and effectively high availability) between the two. Then I messed up, I bought a domain. This was the nail in the coffin. Now that my IP was out there on a hostname I had to ensure I was doing the best practices. I closed all my ports on my RT3200 and decided the Pi would run Nginx Proxy Manager and route all my traffic internally. From there I dove into Traefik which oddly enough lead me to Kubernetes. Once we got comfortable with Kubernetes I was wondering after moving systems many times, "how can I make this easier on me moving forward". The answer was Infrastructure as Code. Ansible was the key to making my life easy. Write it once deploy it a thousand times. Now that I had the code and had the hardware I wanted to get a little networking in. I went and bought the Protectli Vault and slammed pfSense on it. This removed the need for the Proxy Manager on the Pi as Im using HAProxy now to free up the PI (it didn't have much to work with). I then added the managed switch so that I could move away from the RT3200 and still have Ethernet in my lab. Along with that I wanted to seperate my network with VLANs as another layer of security.

## The Future
From here I want to get my hardware in a final revision with official wiki that I can go back and rely on with as much code as possible in GitHub. Once I get that finalized I want to begin heading into Full Stack Engineering (LAMP and stuff) or SRE (devops). Most of my learning has been from [learn-anything.xyz](https://learn-anything.xyz/) and will most likely continue the route of learning by reading/experimenting. I feel like I was relying on YouTube a bit too much and worried about the day there wasn't a video on "x" topic. I am working on becoming better at reading/interpretting developer documentation. Once I have all these things setup and become confident in my knowledge I may end up making a YouTube channel or something to suppliment my wiki for those who wish to learn. 