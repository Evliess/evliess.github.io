---
layout: index
title: "Docker Installation"
category: docker
date: 2018-05-20 15:17:55
---

# 1. Docker Installation

> Environment: Ubuntu 16.04.4 LTS  X86_64bit  

## 1.1 Install docker, docker-compose

1. First, add the GPG key for the official Docker repository to the system:  
```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

2. Add the Docker repository to APT sources:  
```shell
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

3. Next, update the package database with the Docker packages from the newly added repo:  
```shell
sudo apt-get update
```

4. Make sure you are about to install from the Docker repo instead of the default Ubuntu 16.04 repo:  
```shell
apt-cache policy docker-ce
```

5. Finally, install Docker:  
```shell
sudo apt-get install -y docker-ce
```

6. Check that it's running:  
```shell
sudo systemctl status docker
```
Active: active (running) since Fri 2018-05-11 12:26:54 CST;

7. Install docker-compose:  
```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose && sudo chmod +x /usr/local/bin/docker-compose
```

## 1.2 Install docker-machine (Optional)

1. Install docker-machine:  
```shell
base=https://github.com/docker/machine/releases/download/v0.14.0 &&
curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine && sudo install /tmp/docker-machine /usr/local/bin/docker-machine
```

2. Install required linux headers
```shell
sudo apt-get -y install gcc make linux-headers-$(uname -r) dkms
```

3. Add virtualbox repository and key
```shell
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
sudo sh -c 'echo "deb http://download.virtualbox.org/virtualbox/debian $(lsb_release -sc) contrib" >> /etc/apt/sources.list'
```

4. Install virtual box
```shell
sudo apt-get update
sudo apt-get install virtualbox-5.2
```

5. Verify installation
```shell
VBoxManage -v
```

6. Install virtualbox extension package
```shell
curl -O http://download.virtualbox.org/virtualbox/5.2.4/Oracle_VM_VirtualBox_Extension_Pack-5.2.4-119785.vbox-extpack
sudo VBoxManage extpack install Oracle_VM_VirtualBox_Extension_Pack-5.2.4-119785.vbox-extpack
```

7. VBoxManage list extpacks
```shell
VBoxManage list extpacks
```

9. Uninstall virtualbox
```shell
sudo apt-get remove --purge virtualbox 
```

10. Uninstall virtualbox extension
```shell
sudo VBoxManage extpack uninstall "Oracle VM VirtualBox Extension Pack"
vboxmanage list extpacks
```

[Docker技术入门与实践](./images/docker/Docker技术入门与实践.svg)

