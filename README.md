# Project Title: Auld Guard 

## Overview

This project aims to increase my understanding of Linux, Docker, Tor, AI, IDS and Networking by repurposing an old gaming PC into a server that can manage lightweight isolated instances for multiple small scale projects using Docker Engine.

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
  # Verify that the Docker Engine installation
  sudo docker run hello-world

- Aaaaand done! So far, so simple. 

  ![alt text](https://github.com/Wytchwulf/auld-guard/blob/main/Screenshot%20from%202024-04-06%2023-18-04.png)

