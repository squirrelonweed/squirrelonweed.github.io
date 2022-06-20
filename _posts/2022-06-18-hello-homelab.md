---
title: Hello HomeLab
date: 2022-06-18 12:00:00 -400
categories: [homelab,intro]
tags: [cloudflare,pfsense,proxmox,docker,container]    # TAG names should always be lowercase
---

# Introduction

Welcome to Will's Wild Wiki. This is new home for all of my my documentation moving forward. A lot of my development will be out on GitHub but figured I would make easier to read documentation for myself (and hopefully you too). I am also using this site in conjunction with GitHub, LinkedIn and Discord to connect with people who are interested in HomeLabbing. 

In the rest of this article I will go over the hardware and software solutions that I am using in my lab.

# My  HomeLab

After a metric ton of trial and error I have ended up at this config below. Looking forward I would like to get a newer Raspberry Pi but will hold off until prices come down. I may buy a cheap mini PC or Intel NUC to replace it at some point. A few months ago I starting mixing in a cloud resource to add some headaches to my life as well as some good practical experience. In regards to the cloud resources there are plently of places where you can obtain free trials. The only real limit is time as they usually give you plenty of monopoly money to spend. My favorite providers (and cheaper) are [DigitalOcean's](https://try.digitalocean.com/freetrialoffer/) $100, 60 day free trial and [Linode's](https://www.linode.com/pricing/) $50, 60 day free trial. [Google Cloud](https://cloud.google.com/free) also has a completely free tier as well as [$300, 90 free trial](https://cloud.google.com/free/docs/gcp-free-tier/#free-trial) that you can add on.

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
- Cloud of the Month: 1-4 Cores, 1-8GB RAM, 10-100GB Storage

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
