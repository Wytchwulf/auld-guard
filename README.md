# Project Title: Auld Guard 

## Overview

This project aims to increase my understanding of Linux, Docker, Tor, AI, IDS and Networking by repurposing an old gaming PC into a server that can manage lightweight isolated instances for multiple small scale projects using Docker Engine.

Please do not consider the following a tutorial. This is a learning project, not a masterclass. I am exploring systems I am unfamiliar with and liable to make some mistakes along the way.

Wherever relevant I will link the sources I am using for anyone interested in any part of the project. 

## Goals

- 🟢  Start from scratch with a fresh install of [Ubuntu Server 22.04 LTS](https://ubuntu.com/download/server).
- 🟢  Install [Docker Engine](https://docs.docker.com/engine/install/ubuntu/).
- 🔴  Create a Docker Network to run [Wetty](https://github.com/butlerx/wetty), [Nginx](https://nginx.org/en/) and [Certbot](https://github.com/certbot/certbot) for easy access to my server via my [DDNS](https://duckdns.org) on any browser.
- 🔴  Bring it all together with Docker Compose.
- 🔴  Harden security for internet facing port (Let's Encrypt SSL/TLS Encryption, HSTS & CSP headers, UFW, Fail2Ban, Rate Limiting, 2FA, Automation of monitoring, audit logs and updates, Recovery Backup.)
- 🔴  [Kali](https://kali.org/) Instance (maybe [Parrot](https://parrotsec.org/), havent played with that yet)
- 🔴  [Tor](https://torproject.org) Relay with [Nyx](https://nyx.torproject.org) Monitoring
- 🔴  Intrusion Detection with [Suricata](https://suricata.io/)
- 🔴  Run Dolphin Mistral 2.8 Locally with Ollama.
- 🔴  And Beyond?
  
## Table of Contents

- [Project Title: Auld Guard](#project-title-auld-guard)
  - [Overview](#overview)
  - [Goals](#goals)
  - [Table of Contents](#table-of-contents)
  - [Lets Begin!](#lets-begin!)
  - [Docker](#docker)
  - [Docker Compose - Wetty / Nginx / Certbot](#docker-compose)
 
## Lets Begin!

### Clean Slate

This is the easy bit. 

- Headed on over to [Ubuntu Server dowload page](https://ubuntu.com/download/server) and mounted the iso on a USB using [Balena Etcher](https://etcher.balena.io/).
- Dragged the old rig from the deepest darkest corner of my bedroom
- Blew off the dust, powered the old man up and tapped F2 like a madman until I hit BIOS, selected boot from USB, save and reset.
- Followed the instructions, decided against using snap for docker - opting for a clean start, selected option for OpenSSH and.....
- Ubuntu Server up and running, SSH from laptop. 
- Hello, world.

## Docker

- Following the documentation for Docker installation on Ubuntu :
  
- ```bash
  # Add Docker's official GPG key:
  sudo apt-get update
  sudo apt-get install ca-certificates curl
  sudo install -m 0755 -d /etc/apt/keyrings
  sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
  sudo chmod a+r /etc/apt/keyrings/docker.asc
   
  # Add the repository to Apt sources:
  echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  sudo apt-get update

- ```bash
  # Install latest version
  sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

- ```bash
  # Verify the Docker Engine installation
  sudo docker run hello-world

- Aaaaand done! So far, so simple. 

  ![alt text](https://github.com/Wytchwulf/auld-guard/blob/main/Screenshot%20from%202024-04-06%2023-18-04.png)

## Docker Compose - Wetty / Nginx / Certbot

This project is a proof of concept, so I'll not be keeping it. One of the benefits of spinning these projects up on containers is that I can experiment with a variety of programs without the hassle of virtualising entire machines. Just pull an image, run it, and when I'm done just tear it down. 

- First I'll need a network for the containers to communicate in and for the server to communicate to. 
- ```bash
  docker network create web-access-network

- Next up is the docker compose file where I will tell Docker what images to pull and run along with some env details.
- ```yaml
  # sudo vim docker-compose.yml
  services:
    wetty:
      image: freeflyer/wetty
      environment:
        - REMOTE_SSH_SERVER=192.168.100.150
        - REMOTE_SSH_PORT=22
      networks:
        - web-access-network
      ports:
        - "3000:3000"
  
    nginx:
      image: nginx:alpine
      volumes:
        - ./nginx.conf:/etc/nginx/nginx.conf
        - ./certbot/conf:/etc/letsencrypt
        - ./certbot/www:/var/www/certbot
      ports:
        - "80:80"
        - "443:443"
      networks:
        - web-access-network
      depends_on:
        - wetty
  
    certbot:
      image: certbot/certbot
      volumes:
        - ./certbot/conf:/etc/letsencrypt
        - ./certbot/www:/var/www/certbot
      entrypoint: "/bin/sh -c 'trap exit TERM; while :; do certbot renew; sleep 12h & wait $${!}; done;'"
      networks:
        - web-access-network
  
  networks:
    web-access-network:
      external: true

- Now I'll need a config file for the reverse proxy.
- ```yaml
  # sudo vim nginx.conf
  events {}
  
  http {
      include /etc/nginx/conf.d/*.conf;
      include /etc/nginx/mime.types;
  
      # Server for HTTP (port 80) - Redirect all traffic to HTTPS
      server {
          listen 80;
          server_name stingily5411.duckdns.org;
  
          location /.well-known/acme-challenge/ {
              root /var/www/certbot;
              allow all;
          }
  
          location / {
              return 301 https://$host$request_uri;
          }
      }
  
      # Server for HTTPS (port 443)
      server {
          listen 443 ssl;
          server_name stingily5411.duckdns.org;
  
          ssl_certificate /etc/letsencrypt/live/stingily5411.duckdns.org/fullchain.pem;
          ssl_certificate_key /etc/letsencrypt/live/stingily5411.duckdns.org/privkey.pem;
  
          location / {
              proxy_pass http://wetty:3000;
              proxy_http_version 1.1;
              proxy_set_header Upgrade $http_upgrade;
              proxy_set_header Connection "upgrade";
              proxy_set_header Host $host;
          }
      }
  }

- With the reverse proxy in place, insecure connections will be redirected to the https on port 443 where I have ssl certificates set up with LetsEncrypt. To get the certs I needed to request them:
- ```bash
  docker run --rm -v "/home/analyst/certbot/conf:/etc/letsencrypt" -v "/home/analyst/certbot/www:/var/www/certbot" certbot/certbot certonly --webroot --webroot-path=/var/www/certbot -d stingily5411.duckdns.org --email my_email@proton.me --agree-tos --no-eff-email

- Next up to set port forwarding from ports 80 and 443 to the server.

- Finally:
  ```bash
    docker-compose up -d

- And there it is. Just type my DDNS into Firefox... Access to my server from anywhere with access to a browser. 

![alt text](https://github.com/Wytchwulf/auld-guard/blob/main/Screenshot%20from%202024-04-07%2022-24-02.png)

- While it's an interesting idea, I don't like the attack surface that opening up my server to the internet in this way provides. I can harden access with Fail2Ban and restrict access to other areas of my network with UFW and other security measures like Vlan but I just have no need for this as I access my network with Wireguard on a VPN I set up on a Pi so keeping this in place would just add an unnecessary point of failure for my network. 
