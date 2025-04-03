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

### Create no-login user

```
sudo useradd -M -s /usr/sbin/nologin sugar
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

# nginx config
sudo vi /etc/nginx/nginx.conf
```
http {
    # 1. 定义连接控制区域 (基于 IP 地址的限制)
    limit_conn_zone $binary_remote_addr zone=addr:10m;

    # 2. 定义请求速率控制区域 (基于 IP 地址的限制)
    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
```

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
        # 并发连接限制，每个 IP 同时只能有 10 个连接
        limit_conn addr 10;
        # 请求速率限制，每秒允许 1 次请求，突发队列长度为 5
        limit_req zone=one burst=5 nodelay;

        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_connect_timeout 30s;
        proxy_send_timeout 60s;
        proxy_read_timeout 300s;
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
sudo systemctl status nginx
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log

```

## Create a service for your sprintboot app


```bash
# sudo vi /home/jiajia/start.sh

#!/bin/bash
export API_KEY=KEY
java -jar /home/jiajia/app.jar
```

```bash
# sudo vi /etc/systemd/system/sugar.service

[Unit]
Description=Sugar
After=syslog.target
After=network.target[Service]
User=jiajia
Type=simple

[Service]
ExecStart=/home/jiajia/start.sh
Restart=always
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=helloworld

[Install]
WantedBy=multi-user.target


#verify
sudo systemctl start sugar
sudo systemctl status sugar
sudo journalctl -u sugar -f
```

## h2

```
java -cp .\h2-2.3.232.jar org.h2.tools.Shell
java -cp .\h2-2.3.232.jar org.h2.tools.Server -tcp -web
java -cp .\h2-2.3.232.jar org.h2.tools.Server -tcp -tcpPort 9081 -baseDir ~/sugar
```

```sql
CREATE TABLE S_USERS(
    id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    s_name VARCHAR(255),
    s_key VARCHAR(255)
);


CREATE TABLE S_TOKEN(
    id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    token VARCHAR(255),
    s_name VARCHAR(255)
);
CREATE INDEX idx_token on s_token(token);

CREATE TABLE s_audit (
    id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    token VARCHAR(255),
    s_name VARCHAR(255),
    consumed_at Long,
    s_type VARCHAR(15) 
);
CREATE INDEX idx_audit_token on s_audit (s_name);
CREATE INDEX idx_audit_consumed_at on s_audit (consumed_at);

CREATE TABLE s_dict (
    id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    s_name VARCHAR(255),
    s_meaning VARCHAR(512),
    s_type VARCHAR(15),
    s_other VARCHAR(255)
);
CREATE INDEX idx_s_dict on s_dict (s_name);
```

## ssh key-gen

```
1. 生成keys <RAS/ED25519/ECDSA>

openssl genrsa -out C:/keys/rsa_private_key.pem 2048
openssl pkcs8 -topk8 -nocrypt -inform PEM -in C:/keys/rsa_private_key.pem -outform PEM
openssl rsa -in C:/keys/rsa_private_key.pem -pubout -out C:/keys/rsa_public_key.pem

```

## Angular build

```node
npm run build --prod
```

## Run within Springboot

- 1. Copy dist/project_name/browser into resources/static/public/project_name/
- 2. Edit index.html and set ```<base href=".">```
- 3. add addViewControllers

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/public/project_name/welcome")
                .setViewName("forward:/public/project_name/index.html");
    }
}
```


### 提取字体

```
pip install fonttools
pyftsubset ./SourceHanSerifCN-Regular.otf --output-file="SourceHanSansCN-Subset.otf" --unicodes="U+3000-303F, U+4E00-9FFF" --layout-features='*' --flavor="woff2" --with-zopfli                  

```





