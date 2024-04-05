# Project Title: Auld Guard 

## Overview

This project aims to increase my understanding of Linux, Docker, Tor, AI, IDS and Networking by repurposing an old gaming PC into a server that can manage lightweight isolated instances for multiple small scale projects using Docker Engine.

## Goals

- 🟢  Start from scratch with a fresh install of [Ubuntu Server 22.04 LTS](https://ubuntu.com/download/server).
- 🔴  Install [Docker Engine](https://docs.docker.com/engine/install/ubuntu/).
- 🔴  Create a Docker Network to run [Wetty](https://github.com/butlerx/wetty), [Nginx](https://nginx.org/en/) and [Certbot](https://github.com/certbot/certbot) for easy access to my server via my [DDNS](https://duckdns.org) on any browser.
- 🔴  Bring it all together with Docker Compose.
- 🔴  Harden security for internet facing port (Let's Encrypt SSL/TLS Encryption, HSTS & CSP headers, UFW, Fail2Ban, Rate Limiting, 2FA, Automation of monitoring, audit logs and updates, Recovery Backup.)
- 🔴  [Kali](https://kali.org/) Instance (maybe [Parrot](https://parrotsec.org/), havent played with that yet)
- 🔴  [Tor](https://torproject.org) Relay with [Nyx](https://nyx.torproject.org) Monitoring
- 🔴  Intrusion Detection with [Suricata](https://suricata.io/)
- 🔴  An as yet undecided AI project
- 🔴  And Beyond?
  
## Table of Contents

- [Project Title: Auld Guard](#project-title-auld-guard)
  - [Overview](#overview)
  - [Goals](#goals)
  - [Table of Contents](#table-of-contents)
  - [Lets Begin!](#lets-begin!)
 
## Lets Begin!

### Clean Slate

This is the easy bit. 

- Headed on over to [Ubuntu Server dowload page](https://ubuntu.com/download/server) and mounted the iso on a USB using [Balena Etcher](https://etcher.balena.io/).
- Dragged the old rig from the deepest darkest corner of my bedroom
- Blew off the dust, powered the old man up and tapped F2 like a madman until I hit BIOS, selected boot from USB, save and reset.
- Followed the instructions, decided against using snap for docker - opting for a clean start, selected option for OpenSSH and.....
- Ubuntu Server up and running, SSH from laptop. 
- Hello, world. 

