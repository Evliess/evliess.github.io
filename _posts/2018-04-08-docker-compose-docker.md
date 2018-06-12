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
version: '3'
services:
  eureka-server-1:
    image: waitplay/eureka-server:latest
    environment:
      spring.profiles.active: srv1
      eureka.client.serviceUrl.defaultZone: http://172.16.238.12:8762/eureka/
      spring.application.name: Eureka-Server
#preferIpAddress makes sure that Microservices register with their IP address and not with their host name. In a Docker environment host names are unfortunately not easily resolvable by other hosts. This problem is circumvented by the use of IP addresses.
      eureka.instance.preferIpAddress: "true"
      eureka.instance.hostname: eureka-server-1
    ports:
      - "8761:8761"
    container_name: eureka-server-1
    networks:
      app_net:
        ipv4_address: 172.16.238.11


  networks:
  app_net:
    driver: bridge
    ipam:
      driver: default
      config:
      - subnet: 172.16.238.0/24

```
