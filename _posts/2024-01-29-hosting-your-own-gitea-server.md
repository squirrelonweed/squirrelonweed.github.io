---
title: "Hosting Your Own Gitea Server"
date: 2024-01-29 08:00:00 -0500
categories: [Homelab, Cloudlab]
tags: [homelab, cloudlab, container] # TAG names should always be lowercase
---

## Introduction

Today I want to share how I spun up my own Gitea server. I currently ownly use it as a mirror to my GitHub account as an easy way to sync all of my repos in one place. I considered using GitLab or something else but Gitea is super light weight and all I need. The majority of this tutorial can be used in general for beginners to Docker. I suggest learning CLI first but most enjoy GUIs and over the years I have learned to prefer them. By the end of this tutorial you will know how utulize Docker Compose YAML files to spin up your own Nginx Proxy Manager and Gitea server all secured by Cloudflare. I will try to make these instructions as OS agnostic as possible but please understand that all of these steps were performed on Proxmox - LXC VM - Ubuntu 22.04.

## Prerequisites

### Hardware Requirements:

- 1 VM with 2 CPU, 2GB RAM, 8GB HD

> If you are cool with hosting this in the cloud you can easily launch this whole project (and more) with the lower line of shared CPU offerings by offloading your Databases. Something like 1 CPU, 1GB RAM, 5GB HD will do as long as you have a little patience.
{: .prompt-tip }

### Software Requirements:

- Cloudflare
  - DNS record
  - Proxy Enabled
  - API Token for Reverse Proxy
- Docker and Docker-Compose
  - Portainer (optional for you but will be used in this scenario)
  - Nginx Proxy Manager (commonly referred to as NPM)
  - Gitea
- Databases
  - MariaDB
  - MySQL

> Consideration, you could easily use one database for both Gitea and Nginx Proxy Manager as they both support MySQL.
{: .prompt-info }

### What our stack is doing for us:

- Cloudflare will host our DNS record, https://gitea.squirrelonweed.com.
- Docker
  - Portainer will be used to manage our docker containers.
  - Nginx Proxy Manager will be used to manage our reverse proxy along with LetsEncrypt certificates.
  - Docker (via Docker-Compose) will be used to spin up our Gitea server.
- Databases
  - MariaDB will be used for Nginx Proxy Manager.
  - MySQL will be used for Gitea.

## Setting up our Infrastructure

### Cloudflare

Start by logging into the [Cloudflare](https://dash.cloudflare.com/) dashboard. Click on your Domain name. Then head to `Security` -> `WAF` and lets first establish two firewall rules. These will reduce the load on our network, server by automatically block a majority of malicious traffic long before it reaching our local router. Lets make the following firewall rules:

- Name: Block Anything Outside _YOUR_ country

  - If incoming request match: Country, does not equal, _YOUR_ country
  - Then take action: Block

- Name: Challenge Anything Inside _YOUR_ country
  - If incoming request match: Country, equals, _YOUR_ country
  - Then take action: Managed Challenge

![Desktop View](/assets/img/hosting-your-own-gitea-server-cloudflare-waf.png){: width="700" height="500" }
_An image showing my Cloudflare WAF in action._

Now lets head to `SSL` on the left and ensure that `Your SSL/TLS encryption mode` is `Full`. If not click on the checkbox to the right and choose `Full`.

From here lets click on `DNS` -> `Records` and create a new DNS record using our domain. In this example I created an A record for `gitea.squirrelonweed.com` pointing to my local WAN address. I also chose to enable the `Proxy`.

> Another reason I enjoy utilizing cloud resources is that they have an easily available public IP with easy to configure firewalls. No need to worry about configuring your local router to forward ports 80, 443 to Nginx Proxy Manager.
{: .prompt-tip }

Final thing to do on Cloudflare is to obtain an API Token to the Cloudflare zone (Domain) so that we can use it later in our setup with Nginx Proxy Manager. Thankfully Cloudflare hosts a [tutorial](https://developers.cloudflare.com/fundamentals/api/get-started/create-token/) of their own that is very easy to follow. Please do so and keep track of the API Token for your domain, remember it is only shown once on the UI.

### VM

Alrighty, now we have all of our external requirements completed on cloudflare and we can full focus on the homelab. First lets get our host up and going. Lets fire up the distribution you enjoy the most and [install Docker](https://docs.docker.com/engine/install/) on it. Once done we need to get [Portainer installed](https://docs.portainer.io/start/install-ce/server/docker). Once done you should be able to open your browser and navigate to `https://IP_OF_YOUR_VM:9443`. You may see a warning that your connection is not secure. Don't worry that is just telling you that security is in place but an external certificate authoritiy could not verify the self signed on your VM. You can safely ignore that and continue to the login screen. Once there setup an account and see you on the the dashboard.

To utilize more of the Gitea features and increase our security posture we still want to setup a service account for Gitea. We will need to run the following commands to setup the `git` account and SSH shim for the Gitea SSH service.

- `sudo adduser --system --shell /bin/bash --gecos 'Git Version Control' --group --disabled-password --home /home/git git`
- `sudo -u git ssh-keygen -t rsa -b 4096 -C "Gitea Host Key"`
- `sudo -u git cat /home/git/.ssh/id_rsa.pub | sudo -u git tee -a /home/git/.ssh/authorized_keys`
- `sudo -u git chmod 600 /home/git/.ssh/authorized_keys`
- copy snippit block below as one copy / paste:

```
cat <<"EOF" | sudo tee /usr/local/bin/gitea
#!/bin/sh
ssh -p 222 -o StrictHostKeyChecking=no git@127.0.0.1 "SSH_ORIGINAL_COMMAND=\"$SSH_ORIGINAL_COMMAND\" $0 $@"
EOF
```

- `sudo chmod +x /usr/local/bin/gitea`

### Docker

From Portainer first click on `Enviroment` under `Settings` then click on your host. From there modify the `Public IP` to your VM IP. You should be able to copy the `Environment IP` from above. Just remove the port number. This will ensure when you click on a containers port in the Portainer dashboard it will open up in your browser to the correct IP. Useful for any Portainer setup. Especially so if you end up liking portainer and connecting anything that runs docker to it.

![Desktop View](/assets/img/hosting-your-own-gitea-server-portainer.png){: width="700" height="700" }
_An image showing my Portainer Agents on the dashboard._

Now let click on `Stacks` -> `+ Add Stack` and lets begin creating docker-compose files. We will be creating multiple stacks so that when we do maintenance we can take down parts instead of a while within an individual `Stack` UI. Below will be the following docker-compose files for your use. I will link to the official ones if the owner has one.

Stack #1 - [Nginx Proxy Manager](https://nginxproxymanager.com/setup/#using-mysql-mariadb-database):

```
version: '3.8'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    container_name: npm
    restart: unless-stopped
    ports:
      # These ports are in format <host-port>:<container-port>
      - '80:80' # Public HTTP Port
      - '443:443' # Public HTTPS Port
      - '81:81' # Admin Web Port
      # Add any other Stream port you want to expose
      # - '21:21' # FTP
    environment:
      # Mysql/Maria connection parameters:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "npm"
      DB_MYSQL_PASSWORD: "npm"
      DB_MYSQL_NAME: "npm"
      # Uncomment this if IPv6 is not enabled on your host
      # DISABLE_IPV6: 'true'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    depends_on:
      - db

  db:
    image: 'jc21/mariadb-aria:latest'
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: 'npm'
      MYSQL_DATABASE: 'npm'
      MYSQL_USER: 'npm'
      MYSQL_PASSWORD: 'npm'
      MARIADB_AUTO_UPGRADE: '1'
    volumes:
      - ./mysql:/var/lib/mysql
```

Stack #2 - [Gitea](https://docs.gitea.com/next/installation/install-with-docker):

```
version: "3"

networks:
  gitea:
    external: false

services:
  server:
    image: gitea/gitea:1.21.4
    container_name: gitea
    environment:
      - USER_UID=1000 # Change to the git UID from earlier
      - USER_GID=1000 # Change to the git GID from earlier
      - GITEA__database__DB_TYPE=mysql
      - GITEA__database__HOST=db:3306
      - GITEA__database__NAME=gitea
      - GITEA__database__USER=gitea
      - GITEA__database__PASSWD=gitea
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "222:22"
    depends_on:
      - db

  db:
    image: mysql:8
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=gitea
      - MYSQL_USER=gitea
      - MYSQL_PASSWORD=gitea
      - MYSQL_DATABASE=gitea
    networks:
      - gitea
    volumes:
      - ./mysql:/var/lib/mysql

```

> Please consider setting a volumes to full paths such as `/home/yourname/gitea:/data` and changing all of your database passwords something unique and secure. Remember that if you change the passwords to mirror that to the associated container. Even better would be to use [Portainer's example](https://www.portainer.io/blog/using-env-files-in-stacks-with-portainer) of how to do so.
{: .prompt-tip }

If everything went well we have a total of two stacks and four containers. If everything did not, click on the `logs` icon under `Quick Actions` of each container `Containers` and begin using this information to see what may be awry. As long as everything is good lets configure our new services.

#### Services

In Portainer begin by clicking on `Containers` in the top left. From there you should see the four containers (npm, npm-db, gitea, gitea-db). To the right of `npm` click on the port `81:81`. In the new window login to NPM with the following credentials:

- Email: admin@example.com
- Password: changeme

Once logged in head to `Proxy Hosts` then click on `Add Proxy Host`. Input the following information:

- Details
  - Domain Name: gitea.squirrelonweed.com
  - Scheme: http
  - Forward Hostname / IP: The IP address of the host running the gitea container.
  - Forward Port: 3000
  - Enable the following options: Block Common Exploits, Websockets Support

![Desktop View](/assets/img/hosting-your-own-gitea-server-npm-details.png){: width="400" height="400" }
_Nginx Proxy Manager Proxy Host Details Page._

- SSL
  - SSL Certificate: Request a new SSL Certificate with Lets Encrypt
  - Check DNS Challenge. For the provider choose Cloudflare and input the API Token from earlier.
  - Enable the following options: Block Common Exploits, Websockets Support

![Desktop View](/assets/img/hosting-your-own-gitea-server-npm-ssl.png){: width="400" height="700" }
_Nginx Proxy Manager Proxy Host SSL Page._

If everything went well you should be returned back to the list of Proxy Hosts and you should see the new gitea.squirrelonweed.com proxy host listed. Feel free to click on it to be sent to the setup page of your new private gitea service. Proceed to complete the Gitea Initial Setup. The important boxes to be sure are filled correctly are:

- Database Settings
  - Database Type: MySQL
- General Settings
  - Server Domain: gitea.squirrelonweed.com
  - Gitea Base URL: https://gitea.squirrelonweed.com

Once done you should be taken to the home page of Gitea where you can begin creating repositories. I created an API token on GitHub that I use to create `mirror` repositories on Gitea. The benefit here is that it can see both public and private repositories.

## What is Next

Now that you have a Gitea Server with a public DNS record that can be reached from anywhere in the world (provided youadjust the Cloudflare WAF rule for Block accordingly). You can effectively write code and back it up on your own servers now. I have ran this entire project on everything from a Raspberry Pi, Mini PC and Cloud Provider. I left a new challenges throughout this tutorial for you such as `how to make both containers use a single DB`. I am excited to hear what you come up with. Let me know via any of my social medias.

#### Bonus

For those of you who made it to the bottom here is my favorite and lightweight version this project. I tested on a 1 CPU, 1GB RAM, 50GB SSD free trial cloud VM, free online DB trial (MANY available of both resources from major providers, with a student/university email) and SQLlite for NPM. Sadly NPM has no support for Postgres. It takes about 2 minutes or so to get everything downloaded / configured but totally doable as a proof of concept.

```
version: "3"

networks:
  gitea:
    external: false

services:
  server:
    image: gitea/gitea:latest
    container_name: gitea
    restart: unless-stopped
    environment:
      - USER_UID=110
      - USER_GID=118
      - GITEA__database__DB_TYPE=postgres
      - GITEA__database__HOST='xy-broken-quails-65258210.us-west-5.aws.provider.com'
      - GITEA__database__NAME='squirrelongitea'
      - GITEA__database__USER='squirrelonweed'
      - GITEA__database__PASSWD='GETFROMNEONDB'
    volumes:
      - /home/git/gitea:/data
      - /home/git/.ssh/:/data/git/.ssh
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "222:22"
    networks:
      - gitea

  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
```
