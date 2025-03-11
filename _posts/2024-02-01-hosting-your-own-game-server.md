---
title: "Hosting Your Own Game Server with Pterodactyl and Nginx Proxy Manager"
date: 2024-02-01 08:00:00 -0500
categories: [Homelab]
tags: [homelab, container] # TAG names should always be lowercase
---

### Installation Instructions

Today we will be going over how to install a game server ran on the open source software [Pterodactyl](https://github.com/pterodactyl/panel). This has been one of my favorite platforms to host game servers that have a solid web GUI. The ability to share the ownership with friends and portability of docker makes this an excellent choice. Pleaes remember that you are installing a `GAME SERVER`. These are resource intensive and the full stack will not run on anything weaker than an RPi 5. We will go over how to mitigate this below. Alright, lets get into it!

![Desktop View](/assets/img/2024-02-01-hosting-your-own-game-server-panel.png){: width="800" height="800" }
_An image showing the Pterodactyl Running server._

#### Prerequisites

- 2 VMs
- 1 Domain hosted on Cloudflare

#### Network Setup

The biggest potential dealbreaker here is that you will absolutely need to have the ability to forward ports on your router for the game servers along with Nginx Proxy Manager. If you do not have the ability you may be able to use a VPN or service like Cloudflare Tunnels but I have not personally investigated using either. Your milage may vary.

#### Cloudflare Setup

Lets head to your DNS records and create two records. Depends on your homelab setup you wil want to either create an `A` record or a `CNAME` if you are using DDNS. The two hostnames we will be using are `panel.squirrelonweed.com` and `wings01.squirrelonweed.com`. You may leave the proxy on for both but there from what I have read there is no upside to proxying the wings server. Having the wings server proxied will also prevent SFTP as it thinks everything is a Cloudflare IP. I personally proxy both.

#### Hardware Considerations

Running the Panel is very easy enough to run. Nginx Proxy Manager (NPM)is also lightweight. The resource restraints kick in when running the Wings server alongside them on the same physical host. I recommend running the Wings server from a second physical host. If you are resource contraint and your hypervisor supports it such as Proxmox consider using LXC containers to save memory for your games.

Single Host recommended hardware configuration:

- 1 VM with 4+ vCPU, 8GB+ RAM, 50GB+ HD

Multi Host recommended hardware configuration:

- NPM:
  - 1 VM with 1 vCPU, 1GB RAM, 8GB HD
- Panel:
  - 1 VM with 1 vCPU, 1GB RAM, 8GB HD
- Wings:
  - 1 VM with 4+ vCPU, 8GB+ RAM, 100GB HD

> If you choose the single host setup you will have issues with the port 443 being in reserve as everything will leverage it in the end.
{: .prompt-tip }

#### Setup Linux

For most of these steps we will not have to utilize the CLI other than to run `sudo docker-compose run --rm panel php artisan p:user:make` later down the road. Following these instructions you should be able to spin up via Portainer (for the most part lol) or CLI. If you went with the multi host setup please make sure you bounce around from your `panel` to `wings` host as needed. If you find a way to exec this on Portainer let me know and I will update this article.

- `mkdir pterodactyl`

- `cd pterodactyl`

- `mkdir panel`

- `cd panel`

- `nano docker-compose.yml`

Copy the contents of the docker-compose file below to ~/pterodactyl/panel/docker-compose.yml.

```
version: '3.8'
x-common:
  database:
    &db-environment
    # Do not remove the "&db-password" from the end of the line below, it is important
    # for Panel functionality.
    MYSQL_PASSWORD: &db-password "CHANGE_ME"
    MYSQL_ROOT_PASSWORD: "CHANGE_ME_TOO"
  panel:
    &panel-environment
    # This URL should be the URL that your reverse proxy routes to the panel server
    APP_URL: "https://panel.squirrelonweed.com"
    # A list of valid timezones can be found here: http://php.net/manual/en/timezones.php
    APP_TIMEZONE: "America/New_York"
    APP_SERVICE_AUTHOR: "youremail@gmail.com"
    TRUSTED_PROXIES: "*" # Set this to your proxy IP
    # Uncomment the line below and set to a non-empty value if you want to use Let's Encrypt
    # to generate an SSL certificate for the Panel.
    # LE_EMAIL: ""
  mail:
    &mail-environment
    MAIL_FROM: "youremail@gmail.com"
    MAIL_DRIVER: "smtp"
    MAIL_HOST: "mail"
    MAIL_PORT: "25"
    MAIL_USERNAME: "youremail@gmail.com"
    MAIL_PASSWORD: "yourpassword"
    MAIL_ENCRYPTION: "true"

#
# ------------------------------------------------------------------------------------------
# DANGER ZONE BELOW
#
# The remainder of this file likely does not need to be changed. Please only make modifications
# below if you understand what you are doing.
#
services:
  database:
    image: mariadb:10.5
    restart: always
    command: --default-authentication-plugin=mysql_native_password
    volumes:
      - "/srv/pterodactyl/database:/var/lib/mysql"
    environment:
      <<: *db-environment
      MYSQL_DATABASE: "panel"
      MYSQL_USER: "pterodactyl"
  cache:
    image: redis:alpine
    restart: always
  panel:
    image: ghcr.io/pterodactyl/panel:latest
    restart: always
    ports:
      - "80:80"
      - "443:443"
    links:
      - database
      - cache
    volumes:
      - "/srv/pterodactyl/var/:/app/var/"
      - "/srv/pterodactyl/nginx/:/etc/nginx/http.d/"
      - "/srv/pterodactyl/certs/:/etc/letsencrypt/"
      - "/srv/pterodactyl/logs/:/app/storage/logs"
    environment:
      <<: [*panel-environment, *mail-environment]
      DB_PASSWORD: *db-password
      APP_ENV: "production"
      APP_ENVIRONMENT_ONLY: "false"
      CACHE_DRIVER: "redis"
      SESSION_DRIVER: "redis"
      QUEUE_DRIVER: "redis"
      REDIS_HOST: "cache"
      DB_HOST: "database"
      DB_PORT: "3306"
networks:
  default:
    ipam:
      config:
        - subnet: 172.20.0.0/16
```

- `mkdir pterodactyl`

- `cd pterodactyl`

- `mkdir wings`

- `cd wings`

- `nano docker-compose.yml`

Copy the contents of the docker-compose file below to ~/pterodactyl/wings/docker-compose.yml.

```
version: '3.8'

services:
  wings:
    image: ghcr.io/pterodactyl/wings:latest
    restart: always
    networks:
      - wings0
    ports:
      - "8080:8080"
      - "2022:2022"
      - "443:443"
    tty: true
    environment:
      TZ: "America/New_York"
      WINGS_UID: 988
      WINGS_GID: 988
      WINGS_USERNAME: pterodactyl
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "/var/lib/docker/containers/:/var/lib/docker/containers/"
      - "/etc/pterodactyl/:/etc/pterodactyl/"
      - "/var/lib/pterodactyl/:/var/lib/pterodactyl/"
      - "/var/log/pterodactyl/:/var/log/pterodactyl/"
      - "/tmp/pterodactyl/:/tmp/pterodactyl/"
      - "/etc/ssl/certs:/etc/ssl/certs:ro"
      # you may need /srv/daemon-data if you are upgrading from an old daemon
      #- "/srv/daemon-data/:/srv/daemon-data/"
      # Required for ssl if you use let's encrypt. uncomment to use.
      #- "/etc/letsencrypt/:/etc/letsencrypt/"
networks:
  wings0:
    name: wings0
    driver: bridge
    ipam:
      config:
        - subnet: "172.21.0.0/16"
    driver_opts:
      com.docker.network.bridge.name: wings0
```

#### Install Docker

`curl -fsSL https://get.docker.com -o get-docker.sh`

`sudo sh get-docker.sh`

#### Install Portainer

We can use Portainer to manage our Panel and Wings container from a secure GUI with minimal headroom.

- `sudo docker volume create portainer_data`

- `sudo docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest`

#### Install Nginx Proxy Manager

Click on `Stacks` -> `+ Add Stack` and lets begin creating docker-compose files. We will be creating multiple stacks so that when we do maintenance we can take down parts instead of a while within an individual `Stack` UI. Below will be the following docker-compose files for your use. I will link to the official ones if the owner has one.

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

In Portainer begin by clicking on `Containers` in the top left. From there you should see the four containers (npm, npm-db, panel, wings). To the right of `npm` click on the port `81:81`. In the new window login to NPM with the following credentials:

- Email: admin@example.com
- Password: changeme

You will be prompted to change your password. Please make this something secure. Once logged in head to `Proxy Hosts` then click on `Add Proxy Host`. Input the following information:

- Details

  - Domain Name: panel.squirrelonweed.com
  - Scheme: http
  - Forward Hostname / IP: The IP address of the host running the panel container.
  - Forward Port: 80
  - Enable the following options: Block Common Exploits, Websockets Support

- SSL

  - SSL Certificate: Request a new SSL Certificate with Lets Encrypt
  - Check DNS Challenge. For the provider choose Cloudflare and input your Cloudflare API Token.
  - Enable the following options: Block Common Exploits, Websockets Support

- Advanced
  - proxy_set_header X-Real-IP $remote_addr;
  - proxy_set_header Host $host;
  - proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  - proxy_set_header X-Forwarded-Proto $scheme;
  - proxy_redirect off;
  - proxy_buffering off;
  - proxy_request_buffering off

If everything went well you should be returned back to the list of Proxy Hosts and you should see the new panel.squirrelonweed.com proxy host listed. Next we need to create a record for the wings server as well. Create a record using the following information:

- Details

  - Domain Name: wings01.squirrelonweed.com
  - Scheme: http
  - Forward Hostname / IP: The IP address of the host running the wings container.
  - Forward Port: 443
  - Enable the following options: Block Common Exploits, Websockets Support

- SSL
  - SSL Certificate: Request a new SSL Certificate with Lets Encrypt
  - Check DNS Challenge. For the provider choose Cloudflare and input your Cloudflare API Token.
  - Enable the following options: Block Common Exploits, Websockets Support

Now if everything went well you should bereturned to the list of Proxy Hosts and you should see the new wings01.squirrelonweed.com proxy host listed along side the panel.squirrelonweed.com proxy host. Now we have finished setting up our network configuration. We are just a few containers away from running a Minecraft server for your friends.

#### Install Pterodactyl Panel

Click on `Stacks` -> `+ Add Stack` and lets begin creating following docker-compose file below:

```
version: '3.8'
x-common:
  database:
    &db-environment
    # Do not remove the "&db-password" from the end of the line below, it is important
    # for Panel functionality.
    MYSQL_PASSWORD: &db-password "CHANGE_ME"
    MYSQL_ROOT_PASSWORD: "CHANGE_ME_TOO"
  panel:
    &panel-environment
    # This URL should be the URL that your reverse proxy routes to the panel server
    APP_URL: "https://panel.squirrelonweed.com"
    # A list of valid timezones can be found here: http://php.net/manual/en/timezones.php
    APP_TIMEZONE: "America/New_York"
    APP_SERVICE_AUTHOR: "youremail@example.com"
    TRUSTED_PROXIES: "*" # Set this to your proxy IP
    # Uncomment the line below and set to a non-empty value if you want to use Let's Encrypt
    # to generate an SSL certificate for the Panel.
    # LE_EMAIL: ""
  mail:
    &mail-environment
    MAIL_FROM: ""
    MAIL_DRIVER: "smtp"
    MAIL_HOST: "mail"
    MAIL_PORT: "25"
    MAIL_USERNAME: ""
    MAIL_PASSWORD: ""
    MAIL_ENCRYPTION: "true"

#
# ------------------------------------------------------------------------------------------
# DANGER ZONE BELOW
#
# The remainder of this file likely does not need to be changed. Please only make modifications
# below if you understand what you are doing.
#
services:
  database:
    image: mariadb:10.5
    restart: always
    command: --default-authentication-plugin=mysql_native_password
    volumes:
      - "/srv/pterodactyl/database:/var/lib/mysql"
    environment:
      <<: *db-environment
      MYSQL_DATABASE: "panel"
      MYSQL_USER: "pterodactyl"
  cache:
    image: redis:alpine
    restart: always
  panel:
    image: ghcr.io/pterodactyl/panel:latest
    restart: always
    ports:
      - "80:80"
      - "443:443"
    links:
      - database
      - cache
    volumes:
      - "/srv/pterodactyl/var/:/app/var/"
      - "/srv/pterodactyl/nginx/:/etc/nginx/http.d/"
      - "/srv/pterodactyl/certs/:/etc/letsencrypt/"
      - "/srv/pterodactyl/logs/:/app/storage/logs"
    environment:
      <<: [*panel-environment, *mail-environment]
      DB_PASSWORD: *db-password
      APP_ENV: "production"
      APP_ENVIRONMENT_ONLY: "false"
      CACHE_DRIVER: "redis"
      SESSION_DRIVER: "redis"
      QUEUE_DRIVER: "redis"
      REDIS_HOST: "cache"
      DB_HOST: "database"
      DB_PORT: "3306"
networks:
  default:
    ipam:
      config:
        - subnet: 172.20.0.0/16
```

Alternatively if you are using CLI run the following commands:

- `wget -O ~/pterodactyl/panel/docker-compose.yaml (repo not yet publicly available, nano file above)

- `cd ~/pterodactyl/panel`

- `sudo docker-compose up -d`

Check the contents of Then execute the following command on your panel instance to create the first user:

- `sudo docker-compose run --rm panel php artisan p:user:make`

Login to your Panel by visiting your domain name. Visit `Admin` (gear icon in top right) -> `Locations` and then create a new location. Once done click on `Nodes` and lets create our Wings config.yml file. Fill in the following details:

- Name: `Wing01`
- Location: Same as created in the earlier step.
- FDQN: `wings01.squirrelonweed.com`
- Total Memory: `8192`
- Total Disk Space: `16384`
- Daemon Port: `443`

![Desktop View](/assets/img/2024-02-01-hosting-your-own-game-server-new-node.png){: width="700" height="700" }
_An image showing the Panel New Node Config page filled in._

Click on Create Node in the bottom right corner. Now click on configuration and copy the config.yml file made for your node specified above. If you do not see that it is HTTPS you will need to go back and modify your panel's .env file (located at /srv/pterodactyl/var/.env) and set it to APP_URL to https. The list of trusted proxies is Cloudflare's Flexible SSL. Below are the modified .env file contents:

- `APP_URL="https://panel.squirrelonweed.com"`

- `TRUSTED_PROXIES=YOURIP/CIDR,173.245.48.0/20,103.21.244.0/22,103.22.200.0/22,103.31.4.0/22,141.101.64.0/18,108.162.192.0/18,190.93.240.0/20,188.114.96.0/20,197.234.240.0/22,198.41.128.0/17,162.158.0.0/15,104.16.0.0/13,104.24.0.0/14,172.64.0.0/13,131.0.72.0/22`

#### Install Pterodactyl Wings

Alrighty, back to configuring our Wings server. Now lets reload your brower and head back to `Admin` -> `Nodes` -> `Wings01` and click on Configuration. You should see the last line now shows HTTPS. Lets copy the whole thing and run the following commands:

- `sudo nano /etc/pterodactyl/wings/config.yml`

- Copy the contents of the config.yml from the Panel and save the file.

Click on `Stacks` -> `+ Add Stack` and lets begin creating following docker-compose file below:

```
version: '3.8'

services:
 wings:
   image: ghcr.io/pterodactyl/wings:latest
   restart: always
   networks:
     - wings0
   ports:
     - "8080:8080"
     - "2022:2022"
     - "443:443"
   tty: true
   environment:
     TZ: "America/New_York"
     WINGS_UID: 988
     WINGS_GID: 988
     WINGS_USERNAME: pterodactyl
   volumes:
     - "/var/run/docker.sock:/var/run/docker.sock"
     - "/var/lib/docker/containers/:/var/lib/docker/containers/"
     - "/etc/pterodactyl/:/etc/pterodactyl/"
     - "/var/lib/pterodactyl/:/var/lib/pterodactyl/"
     - "/var/log/pterodactyl/:/var/log/pterodactyl/"
     - "/tmp/pterodactyl/:/tmp/pterodactyl/"
     - "/etc/ssl/certs:/etc/ssl/certs:ro"
     # you may need /srv/daemon-data if you are upgrading from an old daemon
     #- "/srv/daemon-data/:/srv/daemon-data/"
     # Required for ssl if you use let's encrypt. uncomment to use.
     #- "/etc/letsencrypt/:/etc/letsencrypt/"
networks:
 wings0:
   name: wings0
   driver: bridge
   ipam:
     config:
       - subnet: "172.21.0.0/16"
   driver_opts:
     com.docker.network.bridge.name: wings0
```

Alternatively if you are using CLI run the following commands:

- `wget -O ~/pterodactyl/panel/docker-compose.yaml (repo not yet publicly available, nano file above)

- `cd ~/pterodactyl/wings`

- `docker-compose up --force-recreate`

Head back to your Panel and click on `Admin` -> `Nodes` and you should see a green heart next to it. If so you have successfully installed Pterodactyl Panel and Wings via Docker. The last things to do are:

![Desktop View](/assets/img/2024-02-01-hosting-your-own-game-server-nodes.png){: width="1000" height="146" }
_Example of two nodes that are successfully connected a a panel._

- Setup port forwarding for any potential server ports
  - Then place the list of ports from the step above on Node's Allocated Ports (`Panel` -> `Admin` -> `Nodes` -> `Wings01` -> `Allocation`)
- Add [Official Eggs](https://github.com/parkervcp/eggs) to the Panel.
- Stop building infrastructure and start playing games.

#### What is Next?

Who knows... sometime I find a product so cool I feel the need to immediately share. Other times I need to dive into the product and see if it is even worth downloading onto a hard drive. I do think I want to setup a cybersecurity analysis setup using Wazuh and Copilot but I want both products to mature a bit more. Thanks for reading!
