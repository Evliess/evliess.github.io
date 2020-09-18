---
layout: index
title: "Setup Jenkins in docker"
category: devops
date: 2019-09-20 12:17:55
---


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


