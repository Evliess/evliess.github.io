---
layout: index
title: "Docker Compose"
category: docker
date: 2018-06-05 22:17:55
---

# 1. Docker Compose Installation

> Environment: Ubuntu 16.04.4 LTS  X86_64bit  

## 1.1 Install docker-compose

1. Download the latest version of Docker Compose:
```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
```

2. Apply executable permissions to the binary:  
```shell
sudo chmod +x /usr/local/bin/docker-compose
```

3. Install command completion:  
```shell
sudo curl -L https://raw.githubusercontent.com/docker/compose/1.21.2/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
```

4. Test the installation:  
```shell
docker-compose --version
```

# 2. Docker Compose Useful Command
```shell
//Starts the containers in the background and leaves them running
docker-compose up -d

//Stops containers and removes containers, networks, volumes, and images created by up
docker-compose down

//Stops running containers without removing them. They can be started again with docker-compose start
docker-compose stop

//Starts existing containers for a service.
docker-compose start
```


# 3. Example Docker Compose File

> 3.1 Configure container network and ip

```shell 
# docker compose version
version: '3'
services:
#service name
  eureka-server-1:
    image: waitplay/eureka-server:latest
    deploy:
      restart_policy:
        condition: on-failure
#runtime environment
    environment:
      spring.profiles.active: srv1
      eureka.client.serviceUrl.defaultZone: http://172.16.238.11:8762/eureka/
      spring.application.name: Eureka-Server
    ports:
      - "8761:8761"
#cofigure docker container IPAddr
    networks:
      app_net:
        ipv4_address: 172.16.238.10

#service name
  eureka-server-2:
    image: waitplay/eureka-server:latest
    deploy:
      restart_policy:
        condition: on-failure
#runtime environment
    environment:
      spring.profiles.active: srv2
      eureka.client.serviceUrl.defaultZone: http://172.16.238.10:8761/eureka/
      spring.application.name: Eureka-Server
    ports:
      - "8762:8762"
#configure docker container IPAddr
    networks:
      app_net:
        ipv4_address: 172.16.238.11

#configure network for docker compose
networks:
  app_net:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 172.16.238.0/24
```
> 3.2 Using server name as host name  

```shell
version: '3'
services:
  eureka-server-1:
    image: waitplay/eureka-server:latest
    deploy:
      restart_policy:
        condition: on-failure
    environment:
      spring.profiles.active: srv1
      eureka.client.serviceUrl.defaultZone: http://eureka-server-2:8762/eureka/
      spring.application.name: Eureka-Server
    ports:
      - "8761:8761"

  eureka-server-2:
    image: waitplay/eureka-server:latest
    deploy:
      restart_policy:
        condition: on-failure
    environment:
      spring.profiles.active: srv2
      eureka.client.serviceUrl.defaultZone: http://eureka-server-1:8761/eureka/
      spring.application.name: Eureka-Server
    ports:
      - "8762:8762"

```
