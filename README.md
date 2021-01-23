# Pi-hole
Messing around with a pihole configuration using docker compose on a raspberry pi.

The docker-compose.yml configuration is set up for a DHCP server to be run on pihole.
Please refer to the following link for further documentation 
https://github.com/pi-hole/docker-pi-hole/ and https://docs.pi-hole.net/



## Requirements

- Install Docker Compose
```
sudo apt-get -y install docker-compose
```

- Create the docker-compose file in a directory of your choosing, in the example below, I create a dir pihole in /home

```
mkdir /home/<user>/pihole
cd /home/<user>/pihole
```

## Spin it up!

- Copy ```docker-compose.yml``` from the repo to the newly created directory ```cd /home/<user>/pihole```

- Spin up the container
```
sudo docker-compose up -d
```

- Check to see that the container is running with the following command
```
sudo docker ps
```

### Additonal Infoz
- If you modify the ```docker-compose.yml``` file, you will need to run the following command for the changes to take effect (If there is a new docker image available, it will also be updated!)
```
sudo docker-compose up --build --detach
```

- Once there is a new pihole docker image built, you can update to the latest by running the following
```
sudo docker-compose up --build --detach
```

- Updating the pihole image without docker compose can be achieved by running the following commands (note: there will be some downtime)

```
sudo docker stop pihole
sudo docker rm -f pihole
sudo docker pull pihole:pihole
sudo docker run <args> pihole
```
