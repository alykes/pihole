# Pi-hole

Welcome to my project, this is a variation of a pihole deployment on a raspberry pi 4B. The deployment consists of the following docker containers:

- pihole
- cloudflared

## Table Of Contents

In this document:

<!-- TOC -->
- [Pi-hole](#pi-hole)
  - [Table Of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Requirements](#requirements)
    - [Install Docker](#install-docker)
    - [Install Docker Compose](#install-docker-compose)
  - [Spin it up](#spin-it-up)
    - [Additional Infoz](#additional-infoz)
  - [Introducing cloudflared](#introducing-cloudflared)
  - [cloudflared and pihole set up](#cloudflared-and-pihole-set-up)
  - [cloudflared tunnel configuration](#cloudflared-tunnel-configuration)
<!-- /TOC -->

Links to other resources:

- [Installation Guide](install.md)
- [Repair Instructions](repair.md)

## Introduction

Messing around with a pihole configuration using docker compose on a raspberry pi.

The docker-compose.yml configuration is set up for a DHCP server to be run on pihole.
Please refer to the following link for further documentation
[docker pi-hole](https://github.com/pi-hole/docker-pi-hole/) and [pi-hole.net](https://docs.pi-hole.net/)

## Requirements

Install Docker and Docker Composer

### Install Docker

```bash
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```  

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### Install Docker Compose

```bash
sudo apt-get -y install docker-compose
```

- Create the docker-compose file in a directory of your choosing, in the example below, I create a dir pihole in /home

```bash
mkdir /home/<user>/pihole
cd /home/<user>/pihole
```

## Spin it up

- Copy `docker-compose.yml` from the repo to the newly created directory `cd /home/<user>/pihole`

- Spin up the container
  
```bash
sudo docker-compose up -d
```

- Check to see that the container is running with the following command

```bash
sudo docker ps
```

### Additional Infoz

- If you modify the `docker-compose.yml` file, you will need to run the following command for the changes to take effect

```bash
sudo docker-compose up --build --detach
```

- Once there is a new pihole docker image built, you can update to the latest by running the following

```bash
sudo docker pull pihole/pihole:latest
sudo docker-compose up --build --detach
```

- Updating the pihole image without docker compose can be achieved by running the following commands (note: there will be some downtime)

```bash
sudo docker stop pihole
sudo docker rm -f pihole
sudo docker pull pihole:pihole
sudo docker run <args> pihole
```

## Introducing cloudflared  

- Added cloudflared configuration to `docker-compose.yaml`  
- To use cloudflared as a proxy for DNS over HTTPs (DoH)

## cloudflared and pihole set up

- Go into `settings -> dns` and in `upstream servers` enter `127.0.0.1#5053` and click `save`  
- Don't forget to remove any ticked `Upstream DNS Servers`  
- Test that you are now issuing DNS over HTTPs requests with the following link [https://one.one.one.one/help/](https://one.one.one.one/help/)  
- You should see `Using DNS over HTTPS (DoH)  Yes`  

## cloudflared tunnel configuration

I will likely be adding this at some point... :)
