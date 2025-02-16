# Installation Instructions

<!-- TOC -->
- [Installation Instructions](#installation-instructions)
  - [Update the OS](#update-the-os)
  - [Create a new user](#create-a-new-user)
  - [SSH Configuration](#ssh-configuration)
  - [Install Docker](#install-docker)
    - [Spin up pihole](#spin-up-pihole)
<!-- /TOC -->

## Update the OS

```bash
sudo apt update
apt list --upgradeable
sudo apt full-upgrade  
```

## Create a new user

- Log in with pi:raspberry and change the password of the default account
`passwd`  

- Add a new user  
  
```bash
sudo adduser <username>
sudo adduser <username> sudo
```

- Add the user to sudoers via visudo  
`sudo visudo`  

- add the following:  
`<user_name> ALL=(ALL:ALL) ALL`  

- disable the pi account  
`sudo usermod --lock --expiredate 1 pi`

## SSH Configuration

- Add a user group  
`sudo groupadd ssh-users`  
- Add the current user to the group  
`sudo usermod -a -G ssh-users $USER`  

On your host machine create a key pair  

```bash
ssh-keygen -t ed25519 -a 777
ssh-copy-id -i <public_key> <username>@<rpi_hostname>
```

> Note: If you are using putty, don't forget to convert the private key using puttygen  

- Change sshd_conf on the Raspberry Pi

```bash
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
UsePAM no
X11Forwarding no
AllowGroups ssh-users
```

- Reload sshd
`sudo systemctl restart sshd`

## Install Docker

[Docker Install](https://docs.docker.com/engine/install/debian/)

```bash
sudo apt-get update

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

```bash
sudo apt-get install docker-compose -y
```

### Spin up pihole  

Refer to the readme.md in the root of this repo  

```bash
sudo docker-compose up -d
sudo docker-compose up --build --detach --remove-orphans
```
