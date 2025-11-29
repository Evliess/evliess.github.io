---
layout: index
title: "Setup Jenkins in docker"
category: devops
date: 2019-09-20 12:17:55
---

# Install docker on Ubuntu 22.04.5 LST

```
# 1. 卸载旧版本（如有）
sudo apt remove docker docker-engine docker.io containerd runc

# 2. 安装依赖
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

# 3. 创建目录
sudo mkdir -p /etc/apt/keyrings

# 4. 下载阿里云提供的 GPG 密钥（替代官方）
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 5. 添加阿里云的 Docker APT 源
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 6. 安装 Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 7. 配置 /etc/docker/daemon.json

cat /etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://docker.1ms.run"
  ]
}

# 8. 重启Docker 服务

sudo systemctl daemon-reload
sudo systemctl restart docker
```

# Run a mysql 8.4 container

```bash
sudo docker pull mysql:8.4
sudo docker run --name mysql -p 3307:3306  -v /var/lib/mysql:/var/lib/mysql -e MYSQL_USER=test_user -e MYSQL_PASSWORD=qq123456 -d mysql:8.4.7
```

# Ubuntu 22.04 安装mysql 8.4.7

- https://dev.mysql.com/downloads/mysql/
- https://dev.mysql.com/downloads/repo/apt/
```
wget https://dev.mysql.com/get/mysql-apt-config_0.8.36-1_all.deb
sudo dpkg -i mysql-apt-config_0.8.36-1_all.deb

sudo apt update
sudo apt install mysql-server

#运行安全初始化（设置 root 密码等）
#sudo mysql_secure_installation
mysql --version
sudo mysql

#添加用户

-- 创建用户（% 表示任意主机）
CREATE USER 'test_user'@'%' IDENTIFIED BY 'qq123456';

-- 授权（这里以 mydb.* 为例，按需调整）
GRANT ALL PRIVILEGES ON mydb.* TO 'test_user'@'%';

-- 刷新权限
FLUSH PRIVILEGES;

# 查看 MySQL 配置,找到数据目录
sudo mysqld --verbose --help | grep "datadir"  
=> /var/lib/mysql/

# Removing MySQL with APT
sudo systemctl stop mysql
sudo apt-get remove mysql-server
sudo apt-get autoremove
```


# How to config jenkins using host JDK and Maven


1\. Download jenkins image  

```
docker pull jenkins/jenkins:lts
```
2\. Custom dockerfile

```
FROM jenkins/jenkins:lts
# if we want to install via apt
USER root
RUN apt-get update && apt-get install -y openjdk-8-jdk maven make && rm -rf /var/lib/apt/lists/*
# drop back to the regular jenkins user - good practice
USER jenkins
```
3\. Build a new image

```
docker build -t waitplay/jenkins:lst .
```
4\. Start a contain to run jenkins

```
docker run -u root -d -p 8080:8080 -p 50000:50000 -v $(which kubectl):/usr/bin/kubectl -v $(which oc):/usr/bin/oc -v /var/jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/usr/bin/docker -v /etc/localtime:/etc/localtime -v $(which docker-compose):/usr/local/bin/docker-compose -e JAVA_OPTS=-Duser.timezone=Asia/Shanghai --name jenkins waitplay/jenkins:latest
```
5\. Download jdk-8u221-linux-x64.tar.gz
. Download [maven](http://us.mirrors.quenda.co/apache/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz)

6\. unzip

```
mkdir -p /var/jenkins_home/globalEnvi
chown -R 1000:1000 jenkins_home

cd /var/jenkins_home/globalEnvi
tar -zxvf jdk-8u221-linux-x64.tar.gz
tar -zxvf apache-maven-3.6.2-bin.tar.gz
```
7\. Set Jenkins -> configuration -> add Environment variables

```
JAVA_HOME=/var/jenkins_home/globalEnvi/jdk1.8.0_221
CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$CLASSPATH
M2_HOME=/var/jenkins_home/globalEnvi/apache-maven-3.6.2
PATH=$JAVA_HOME/bin:$PATH
```
8\. Global Tool Configuration

- Maven Configuration
  xxx/conf/setting.xml
- JDK
- MAVEN

9\. Test shell script

```
java -version
mvn -version
docker -v
```


