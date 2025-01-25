---
layout: index
title: "Ubuntu 20.02 Harden"
category: linux
date: 2025-01-21 15:17:55
---

### Prerequisite

### 环境准备
1. Apply AliYun Server
2. Config security group, permitt 80,443,22 port
3. Apply a domain 
4. 备案


### Update the System

```bash
sudo apt update && sudo apt upgrade -y && sudo apt dist-upgrade && sudo apt autoremove
```



### Install Fail2Ban

```bash
sudo apt install fail2ban -y
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo vi /etc/fail2ban/jail.local

## eidt
[sshd]
enabled = true

sudo systemctl restart fail2ban
```

## Config OS automatically update

```
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

### Setup AppArmor

```bash
sudo apt install apparmor apparmor-utils -y
sudo aa-enforce /etc/apparmor.d/*
```

### Audit (Optional)

```bash
sudo apt install lynis -y
sudo lynis audit system

sudo apt install rkhunter
sudo rkhunter
```

### Disable services (Optional)

```bash
sudo systemctl list-unit-files --type=service | grep enabled

sudo systemctl disable servicename
```


### Configure Firewall with UFW

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 80/tcp 
sudo ufw deny 80/tcp ##deny 80
sudo ufw allow 443/tcp
sudo ufw enable
sudo ufw status verbose
```

### Domain is not reachable

```
Please check security group contains ICMP(IPv4) protocal
```




### Create nonroot user

```
sudo adduser jiajia
sudo usermod -aG sudo jiajia
```

### Config SSH
```bash
sudo vi /etc/ssh/sshd_config

## add below lines
Port 22
PermitRootLogin no
PasswordAuthentication no
AllowUsers jiajia

sudo systemctl restart sshd
```



## Create a service for your sprintboot app

```bash
# sudo vi /etc/systemd/system/hello.service

[Unit]
Description=Spring Boot HelloWorld
After=syslog.target
After=network.target[Service]
User=jiajia
Type=simple

[Service]
ExecStart=/usr/bin/java -DAPI_KEY=sk-9728996874e7443cb8d902328ef5cbb2 -jar /home/jiajia/hello/app.jar
Restart=always
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=helloworld

[Install]
WantedBy=multi-user.target


#verify
sudo systemctl start hello
sudo systemctl status hello
```

## Setup jdk17

```bash
sudo apt install openjdk-17-jre
```

### Setup Nginx

```bash

sudo apt install nginx -y
sudo systemctl status nginx
curl http://localhost

## install ssl/tls cert########

sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com
#optional test renew
sudo certbot renew --dry-run
###############################

####use a self-certificated certs

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/letsencrypt/live/YOU_DOMAIN/self-key.pem \
-out /etc/letsencrypt/live/YOU_DOMAIN/self.crt


# https://blog.csdn.net/hongchen006/article/details/135406416
sudo certbot certonly -d YOU_DOMAIN --manual --preferred-challenges dns
_acme-challenge.YOU_DOMAIN: Xh-RYYirtUKz7r9OR8hDBsg0e8pd7NLWmOVcyMyTPRY

# Certificate is saved at: /etc/letsencrypt/live/YOU_DOMAIN/fullchain.pem
# Key is saved at:         /etc/letsencrypt/live/YOU_DOMAIN/privkey.pem
# This certificate expires on 2025-04-22.

# website config
sudo vi /etc/nginx/sites-available/yourdomain.com

server {
    listen 80;
    server_name YOU_DOMAIN YOU_DOMAIN;
    return 301 https://$host$request_uri;
}
server {
    listen 443 ssl;
    server_name YOU_DOMAIN YOU_DOMAIN;

    # SSL证书路径
    ssl_certificate /etc/letsencrypt/live/YOU_DOMAIN/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/YOU_DOMAIN/privkey.pem;

    # 启用强加密套件
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384';
    ssl_prefer_server_ciphers on;

    # 启用HSTS（强制HTTPS）
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # 防止点击劫持
    add_header X-Frame-Options "SAMEORIGIN";

    # 防止MIME类型嗅探
    add_header X-Content-Type-Options "nosniff";

    # 防止XSS攻击
    add_header X-XSS-Protection "1; mode=block";

    # 禁用服务器版本信息
    server_tokens off;

    # 反向代理到Spring Boot应用
    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # 禁止访问隐藏文件
    location ~ /\. {
        deny all;
    }

    # 禁止访问敏感文件
    location ~* \.(log|sql|bak|conf|ini)$ {
        deny all;
    }
}


sudo systemctl restart nginx
```





